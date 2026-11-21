---
layout: post
title: "Add spoken hints to a Godot puzzle game"
categories: [tutorial, gamedev, godot, open-source]
tags: [godot, godot-4, gdscript, puzzle-game, text-to-speech, game-audio, hint-system, accessibility]
description: "Build a Godot 4 hint panel that reveals one clue at a time as text and optional voice narration. Includes script, audio checks, and testing tips."
---

**A** player is stuck at a locked door. Three stone tiles sit beside it, each marked with a different number of dots. The player wants a nudge, but they might not want the whole answer yet.

Let's build a small hint panel for that moment in Godot 4. Each press of a button reveals one more clue. The clue appears as text and, if the player wants, a voice reads it aloud. You can run the panel as its own scene before adding it to your game.

## Write three hints for the same puzzle

Give each hint a different job. The first directs the player's attention. The second explains what to look for. The third gives the solution.

| Clip | What the player sees and hears |
|------|--------------------------------|
| hint_01 | Look at the three tiles beside the door. |
| hint_02 | Count the dots on each tile. Start with the fewest. |
| hint_03 | Press the tile with two dots, then four, then six. |

Read the lines in order. If the first one gives away the sequence, save that detail for later. A player who only needs a small clue should be able to press the button once and get back to the puzzle. Keep any detail the player needs in the written clue too, so the puzzle works with the sound muted.

You can record the lines yourself or use FlowSpeech's [text to speech](https://flowspeech.io/) editor. Make and download one clip per line, using the same voice for all three. Give the files the names in the table and keep their original extensions. Godot will play these files from the project when the player asks for a hint.

## Check the audio before importing it

Listen to the clips in order with the script out of sight. If "two dots, then four, then six" runs together, add a short pause between the numbers and listen again. Check the start and end of each file too. A long silence at the beginning makes the button feel slow. An abrupt cut can swallow the last word. Catching those now is easier than trying to tell whether a delay came from the file or the scene.

If you already have music for the room, play it behind the clips at the level you expect to use in the game. Listen for the words that carry the clue. In this example, the three numbers need to be clear even while the music is playing. If one is hard to hear, lower the music or make a clearer recording before you move on. Compare the three clips at the same playback volume. If one is much louder than the others, adjust its level so the player doesn't have to reach for the volume control between hints. Copy the finished clips into res://audio/hints/.

## Build the hint panel

Create a scene with a VBoxContainer root named HintPanel. Save it as HintPanel.tscn. Add these four children directly under the root, using the names shown so the script can find them.

```
HintPanel (VBoxContainer)
├── HintText (Label)
├── HintButton (Button)
├── ReadAloud (CheckButton)
└── HintVoice (AudioStreamPlayer)
```

Set the button text to **Show a hint** and the check button text to **Read hints aloud**. Leave the check button off by default so players can try the puzzle quietly. The label can start empty or say "Need a clue?" Give it room to show the longest clue and turn on word wrapping. If the game supports a keyboard or controller, make sure both buttons can receive focus.

Use an AudioStreamPlayer for the spoken clue. That keeps the audio independent of where the player stands, which suits a hint delivered through the interface. Attach the following script to HintPanel. It connects the two button signals when the scene starts.

```gdscript
extends VBoxContainer

@export var hint_clips: Array[AudioStream] = []

const HINTS := [
	"Look at the three tiles beside the door.",
	"Count the dots on each tile. Start with the fewest.",
	"Press the tile with two dots, then four, then six."
]

@onready var hint_text: Label = $HintText
@onready var hint_button: Button = $HintButton
@onready var read_aloud: CheckButton = $ReadAloud
@onready var voice: AudioStreamPlayer = $HintVoice

var hint_index := -1

func _ready() -> void:
	hint_button.pressed.connect(_show_hint)
	read_aloud.toggled.connect(_on_read_aloud_toggled)

func _show_hint() -> void:
	hint_index = mini(hint_index + 1, HINTS.size() - 1)
	hint_text.text = HINTS[hint_index]
	voice.stop()
	if read_aloud.button_pressed and hint_index < hint_clips.size():
		var clip := hint_clips[hint_index]
		if clip != null:
			voice.stream = clip
			voice.play()
	if hint_index == HINTS.size() - 1:
		hint_button.text = "Repeat last hint"
	else:
		hint_button.text = "Show next hint"

func _on_read_aloud_toggled(enabled: bool) -> void:
	if not enabled:
		voice.stop()
```

## Assign the clips in the Inspector

Select HintPanel in the scene tree. In the Inspector, expand **Hint Clips** and set its size to 3. Drag hint_01 from the FileSystem dock into slot 0, hint_02 into slot 1, and hint_03 into slot 2. Check that each clip plays once. If looping is enabled, a clip can keep repeating until the player presses another button or turns narration off.

You can leave a slot empty while you work. The script updates the label before it checks for audio, so the written hint still appears. That lets you test the panel before the recordings are ready. Once the clips are in place, save the scene before running it.

## Try it as a player

Run the scene with **Read hints aloud** switched off. Press **Show a hint**. The first clue should appear in silence. Switch narration on and press **Show next hint**. Listen while you read the second clue. If the voice gives away the full answer at that point, check which file you put in the second slot.

Press again before the second clip finishes. The old clip should stop and the final one should start. Turn narration off while it's playing and make sure the voice stops immediately and the text stays visible. Press **Repeat last hint** with narration on again. The same clue should play without advancing past the end of the list.

Run one more pass at the smallest window size you support. With the longest clue on screen, check that you can still reach both controls. If you support keyboard or controller input, try the full sequence without touching the mouse. It's an easy way to notice a button you can see but can't reach.

## Fix problems and reuse the scene

If the voice is silent with narration enabled and the clip assigned, check whether the audio bus assigned to HintVoice is muted. If Godot reports that it can't find a node, compare its name with the scene tree above. A renamed button or a node nested inside another container will no longer match the path in the script.

Once the panel works on its own, open your puzzle scene and drag HintPanel.tscn from the FileSystem dock into the scene tree. Place it where the player can read the clue and still see the puzzle. Try the first hint in the actual room. A clue like "beside the door" is only helpful if the player can tell which door you mean.

Decide what happens when players close the panel. If you hide it and show it again, it keeps its place. That's useful when someone wants to try the current clue before asking for another. For a different puzzle, make a copy of the scene and its script. Change the lines in HINTS in the copied script, attach it to the new panel, and assign the matching recordings. If both panels use the same script, changing those lines affects both puzzles.
---
layout: post
title: "Self-hosted or SaaS field service software for small teams"
categories: [open-source, tools, business]
tags: [field-service-software, self-hosted, saas, odoo, erpnext, open-source-erp, small-business]
description: "Compare open-source field service software like Odoo and ERPNext with SaaS tools. See the real cost of self-hosting for a small team."
---

Small companies that send technicians into the field (HVAC, plumbing, cleaning, pest control, equipment repair) all need roughly the same software: a schedule, a map, a mobile app for the crew, job reports and some link to invoicing. The SaaS market for this is crowded. So is the open-source side, at least on paper.

If you are the person in the company who "knows computers," someone will eventually ask you whether you can set up a free alternative. This article walks through the realistic options and what they cost once you count your own time.

## The open-source candidates

Three projects come up most often.

**Odoo Community.** Odoo has a large module ecosystem and a polished interface. The field service module, though, belongs to the paid Enterprise edition. In Community you can approximate it with Project, Planning and some community add-ons from the Odoo app store, but you are assembling pieces that were not designed together. The mobile experience for technicians depends on the web interface running on a phone.

**ERPNext.** Frappe's ERP has maintenance visits, service-level agreements and a reasonable data model for equipment and customers. It is fully open source under GPL. Out of the box it is an ERP, though, not a dispatch tool. There is no drag-and-drop dispatch board with a map, and technicians work through the responsive web UI or a custom app you build on Frappe.

**Building your own on a low-code stack.** Some teams glue together NocoDB or Baserow for data, a calendar view and a form tool for job reports. This works for a crew of two. It rarely survives the hire of a fourth technician.

There are smaller projects on GitHub that target field service directly. Most are early stage or maintained by one person, which is a risk for something your whole business runs on.

## The real cost of "free"

The license costs nothing. Everything around it costs something.

**Hosting.** A small VPS that runs ERPNext or Odoo with a handful of users will cost you somewhere between five and forty dollars a month, depending on the provider and how much headroom you want. Add backups to a separate location.

**Setup.** Plan on several days to install, configure user roles, import customers and equipment, and build the job report forms. If you have never deployed Frappe or Odoo, double it.

**Maintenance.** Security updates, version upgrades and the occasional broken module after an upgrade. Odoo major upgrades are known to be painful in Community. Budget a few hours per month at least.

**The mobile gap.** This is where most self-hosted setups struggle. Technicians need an app that opens fast, works with a weak signal, takes photos into the job record and collects a signature. A responsive web page on a phone can do some of this. Offline mode, push notifications and GPS tracking need native code or a lot of custom work.

Put your hourly rate next to these hours. For many small companies, the person doing the setup is the owner or a senior technician, and every hour spent on server maintenance is an hour not spent on paid work.

## What SaaS gives you for the money

Hosted field service apps typically charge per user per month. For a team of five technicians plus an office user, expect a monthly bill comparable to a single tank of fuel for each van.

In return you get a native mobile app, offline mode, a dispatch board with a map, customer notifications and updates you don't have to install. You also get a vendor who can disappear or raise prices, which is the honest downside.

When you compare SaaS tools, the open-source mindset still helps. Check whether the vendor offers:

- An open REST API and webhooks, so your data isn't trapped.
- Full data export in CSV or JSON.
- Integrations through Zapier or similar, in case the built-in ones don't cover your accounting tool.

These points matter more than the feature list. They decide whether you can leave in two years if the product goes in a direction you don't like.

## A practical recommendation

If you already run ERPNext for accounting and inventory and have someone who enjoys maintaining it, extending it for maintenance visits makes sense. You keep everything in one database you control.

If you are starting from spreadsheets and the team is under twenty people, a hosted tool will get you running in days rather than weeks. Pick one with a good API, so you can still connect it to self-hosted systems later. For example, [Planado, a field service app for small teams](https://planado.app/), offers an API, webhooks and a Zapier integration, which lets you push completed jobs into your own database or accounting tool.

Whichever route you choose, run a two-week pilot with real jobs and real technicians before migrating everything. The software that looks best on your laptop is not always the one your crew will open in a cold basement at 7 a.m.

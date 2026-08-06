---
layout: post
title: "Open-source route planning tools for small shippers"
categories: [open-source, tools]
tags: [route-planning, open-source, freight, logistics, openstreetmap, self-hosted, shipping, carrier-comparison]
description: "How small shippers can use open-source routing engines to compare freight options, reduce spreadsheet dependency, and keep operational data in-house."
image: /insights/assets/images/post-images/open-source-route-planning-small-shippers.webp
---

**Small** shippers rarely lack carrier choices. The harder task is putting those choices into one view and working out which route fits the shipment, delivery window and budget. Quotes may arrive in different formats, while distances, border crossings and handling stages still need to be checked separately.

Open-source route planning tools give smaller businesses a way to build part of that comparison process themselves. They can calculate road routes and, in a self-hosted setup, keep routing data on infrastructure controlled by the business. The restrictions they can apply depend on the engine and available map data. They do not replace freight rates, customs knowledge or carrier relationships, but they can make the early planning stage less dependent on disconnected spreadsheets.

## What open-source routing tools can compare

An open-source routing engine starts with the physical journey. It calculates a route between two or more points using [map data](https://www.data.gov.uk/dataset/8321e63d-35ac-498e-acb7-f645ef1658f6/openstreetmap2), road access rules and the vehicle profile supplied by the user. Some engines also allow custom data sources, offline deployment and routing profiles for delivery vehicles or trucks.

![Open-source route planning tools helping small shippers compare freight options](/insights/assets/images/post-images/open-source-route-planning-small-shippers.webp)

For a small shipper, this creates a consistent basis for comparing possible routes. The business can review distance, estimated driving time, border locations and access restrictions without entering each journey into a separate consumer map. With a configurable engine, a developer can assign different weights or penalties to road segments, allowing the system to prefer or avoid certain roads according to the rules set by the business.

That comparison still concerns routes, not complete carrier offers. A routing engine does not know what each carrier will charge next Tuesday, whether space remains on a sailing or which surcharge applies to a particular consignment. Those details must come from another data source.

## Where freight data has to enter the system

To compare carriers, the business needs to connect its routing layer with current service and pricing data. Depending on what carriers provide, this might arrive from an API, a scheduled file, a booking portal export or an internal table maintained by the shipping team.

Outdated or incomplete inputs quickly weaken the comparison. A tool can place three services side by side, but it cannot correct an expired fuel surcharge or notice that a quote excludes customs clearance unless those details have been recorded properly. Transit times also need context. A road journey that appears faster on the map may become slower once terminal cut-off times, ferry schedules or driver restrictions are added.

Once a shipment crosses borders, involves more than one transport mode or requires customs paperwork, small shippers often need [international shipping specialists](https://baxterfreight.com/) to select the right service, coordinate documentation and manage the handovers between carriers. The software can organise route and cost data, but freight expertise is still needed to turn those options into a workable booking.

This distinction matters in European logistics. A shipment between the UK and mainland Europe may involve customs declarations, a ferry or rail connection, local collection and final delivery. The cheapest mapped route does not automatically produce the lowest final cost.

## How carrier comparison works in practice

A useful comparison starts with a small number of fields. The tool needs the collection point, delivery point, weight, dimensions, vehicle requirements and delivery deadline. From there, the routing engine can calculate the road element and return a consistent distance and time estimate.

Carrier data then adds the commercial layer. One service might offer a lower base rate but require delivery to a terminal. Another may cost more but collect directly from the shipper. A third may use groupage, which suits smaller consignments but may follow a fixed departure schedule.

The comparison should separate these differences rather than collapse them into one price. A shipper needs to see the base charge, expected transit time, collection method, customs responsibility and any known surcharges or additional handling costs. Without those fields, the lowest figure can look attractive while hiding work or charges that appear later.

Starting with one regular lane makes the setup easier to test. The business can compare the system's output with recent bookings and identify where the data model is too simple. A route between the same warehouse and customer gives the team a stable case before it adds more countries, carriers or transport modes.

## What open source changes for the business

The strongest argument for open source is control. A self-hosted routing engine keeps the configuration, route logic and operational data within an environment chosen by the business. The team can inspect the code, change the weighting rules and connect the tool to other systems without waiting for a vendor to add a feature.

A shipper could give extra weight to vehicle restrictions, exclude roads unsuitable for a particular load or mark routes that regularly create delays. The same system could feed route data into a warehouse platform, booking interface or internal dashboard.

[Open-source products](https://technology.blog.gov.uk/2026/05/27/adopting-open-source-in-local-government/) do not remove operating costs. The business still pays for hosting, development, monitoring and maintenance, while some map feeds, carrier APIs or commercial datasets may carry additional charges. Spending shifts away from a fixed software licence and towards the infrastructure and technical work needed to run the chosen setup.

That trade-off suits businesses that want control over a repeated process. It is less attractive when the team has no technical support or only compares a few shipments each month.

## What setup and maintenance require

A working system needs more than installing a routing engine. Someone must prepare the map data, configure vehicle profiles, build the interface and decide how carrier information enters the comparison. Authentication, backups and [access permissions](https://dataingovernment.blog.gov.uk/2026/02/27/securing-personal-data-across-government/) also need attention when the tool contains shipment or customer data.

Carrier connections create another maintenance task. APIs change, credentials expire and service names are updated. A file import that works today may fail after a carrier changes its column structure. These issues are manageable, but the business needs clear ownership of them.

Open-source projects usually provide repositories, documentation and issue trackers. Those resources help developers understand the software, but they do not replace internal responsibility. The shipping team still needs to check whether the output reflects current services and real operating conditions.

For this reason, the best first version is often narrow. One route engine, one or two recurring lanes and a limited set of carrier fields will reveal whether the idea saves time. Building a complete international freight forwarding platform before testing the data usually creates more work than value.

## When an open-source setup makes sense

An open-source approach is most useful when a shipper repeats the same comparison often enough to justify technical ownership. Regular movements across several carriers, changing delivery constraints and a need to retain operational data all strengthen the case.

It makes less sense when shipments are occasional, routes change constantly or the business depends heavily on advice from freight partners. In those situations, the cost of maintaining software may exceed the time saved during comparison.

Route planning tools are useful because they make one part of the decision visible. They calculate and organise options in a repeatable way. The final choice still depends on live rates, capacity, customs requirements and the realities of moving freight across borders. For small shippers, the strongest setup combines open-source technology with reliable commercial data and experienced judgement.
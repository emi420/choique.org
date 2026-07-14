---
title: Setting up a first Panoramax instance for Argentina
description: "We have set up the first (public) Panoramax instance in the Americas. It allows the collection of street-level imagery in the Argentine territory"
extra:
  author: Bastian Greshake Tzovaras
date: 2026-05-18
taxonomies:
  tags: ["OSM", "openstreetmap", "panoramax", "street-level imagery"]
---


**This post was [first published on Bastian's blog](https://tzovar.as/setting-up-a-first-panoramax-instance-for-argentina/)** 

Panoramax is an open-source system for creating a commons of street-level imagery, thus creating an openly licensed alternative to Google StreetView etc.
And unlike some of the other alternatives, which openly license the images, the whole software stack itself is free & open source software too.
Plus it is based around the idea of creating a federation of Panoramax servers or instances, similar to the Fediverse of Mastodon, PeerTube, Lemmy, etc.

Since [the first time I posted about Panoramax](https://tzovar.as/open-source-streetview/), the federation has grown quite a bit, to more than 10 instances.
But, as seems (unfortunately ) quite normal for most federated systems, there still is some strong centralisation in terms of usage.
The instances of the French chapter of OpenStreetMap (OSM FR) and the French National Geographic Institute [collect around 97% of all images so far](https://panoramax.fr/stats).
In case of the OSM FR instance this is also due to the fact, that until now they were the only big instance that allowed uploading images from any place on earth, instead of geo-restricting uploads to a region or country.

For Panoramax, such centralisation is not just problematic when thinking about the ability to work in local contexts or the federation's resilience, but also for the sustainability of those individual instances for storage reasons:
Storing millions of high-resolution street-level images eats up a lot of storage.
In the case of the OSM FR instance, the around 51 million pictures take up 120 TB for the raw images, and another 66 TB for derivative images necessary for the efficient display and delivery of them.

And so, a couple of weeks ago, Christian Quest posted on the OSM community forum [that the OSM FR instance is running out of space](https://community.openstreetmap.org/t/osm-fr-panoramax-server-only-for-testing-if-outside-of-france/143428).
He also shared that around half of all of those images come from outside France, contributing to making the status quo not sustainable in the long-term.
And for that reason, future uploads from abroad would very reasonably not be possible.
As someone who did upload quite a lot of images from here in Argentina, I definitely contributed to that problem and it also meant trying to move forward with getting a local instance up more urgently.

## Panoramax in the context of Argentina

Not having a local instance wasn't even necessarily for lack of trying, but there's a number of factors that provide additional barriers:
The first, and maybe biggest one is the sheer cost of hardware.
Electronics in general are extremely expensive in Argentina.
Not just in relative terms, compared to local salaries, but even in absolute terms and outside any "AI"-driven cost explosions, thanks to tarriffs and fees associated with importing.
This means that hardware is generally run until it collapses and means the market for used hardware is not particularly plentiful **and** that the prices for used hardware here often are still higher than what one would have paid for it *new* in places like the EU or the US.
As such, the chances of getting hardware donations, [that other locations can have luck with](https://forum.geocommuns.fr/t/deploying-a-panoramax-instance-the-pre-flight-check-list/1892) for such a project, are also quite slim, especially when starting from scratch.

Similarly, public institutions like universities or municipalities, which in other places might be natural partners for such an effort, while potentially interested, lack the resources to help:
They are all bleeding money under the national government's intentional defunding of all public services.
If you read any German (or are willing to use machine translation) [you can read my own reporting on that topic at Amerika21](https://amerika21.de/autor/bastian-greshake-tzovaras), but otherwise any media that has international coverage should help.
 
The other alternative for getting a Panoramax instance up and running, that a range of other smaller instances seem to use, is self-hosting at home.
But, presence of hardware aside, even that can be not so simple, due to infrastructure constraints.
Especially in more rural contexts like my own:
Beyond regular power cuts or outages, which a UPS could help get around, local internet providers often only offer very slow speeds. 
For example, in my hometown the local cooperative – which manages public utilities including internet connections – offers slow aDSL speeds, with optic fibre being planned to come to *some neighborhoods* at some point in the next year.
That would mean actually using such a Panoramax instance would be sucking HD images through a straw.

All of those factors meant that – despite trying, on and off and with different intensity, since 2024 – we didn't get a local instance of the ground so far.
But with the change to the OSM FR instance generating the necessary urgency it was time to not let the perfect be the enemy of the good any longer.
And while Argentina as a country is huge, maybe it doesn't need hundreds of terabytes to get started?
Firstly, the [actual road network seems to be only ~1/4th of the one of France](https://en.wikipedia.org/wiki/List_of_countries_by_road_network_size).
And secondly, the number of local contributors that upload imagery, at least as estimated from the OSM FR instance, is quite manageable!

## Picking a setup and making an instance

Based on this, I wondered if it would maybe not be possible to just rent a *"small"* dedicated server somewhere, just to get things off the ground.
Given the hardware costs, see above, it would have to be a hoster outside the country, as local options are also quite expensive.
Which isn't ideal latency-wise, but beggars can't be choosers.
And so I started brainstorming more concretely with [Daniel](https://social.coop/@dbellomo) and [Marcos](https://en.osm.town/@mdione) about options and how we could setup an instance.

To make use of his extensive first-hand Panoramax-hosting experience I also reached out to Christian Quest, who pointed out that French hoster *OVH* actually [offers a dedicated server-type](https://eco.ovhcloud.com/en/kimsufi/ks-stor/) that's somewhat affordable at US $26.60/month and comes with four 4TB hard drives that can be setup into a software RAID and 500 GB SSD for storing the database etc[^1].
In a RAID5 setup, this would give 12 TB of usable storage for the images (or 16 TB, when paying slightly more for an option of four 6TB drives), and the rest of the specifications would also be enough to run an instance.
At least, as long as one makes use of the image blurring service that OSM FR graciously runs as a service, as the suggested machine type doesn't have a dedicated GPU.
In the 16 TB setup, this should be enough to store around 4 million images, assuming the same mix of image sources/qualities that OSM FR stores.

That looked like it should last for at least a bit, as so far around 830,000 images taken in Argentina have been uploaded to the OSM FR instance over the past 2 years.
And so we decided to go ahead and give it a try, to get us started and hopefully allows us to create a convincing demo.
Which in turn can hopefully help getting into more targeted conversations for support in the future.

With the decision for the hosting type made, the actual setup to get us started wasn't too hard either:
The Panoramax team made a [great documentation about deploying with `docker compose`](https://docs.panoramax.fr/backend/install/tutorials/running_docker_osm_auth/) and using OSM through OAuth as a login provider.
And we benefitted even more from [the detailed write-up by Matija Nalis](https://www.openstreetmap.org/user/Matija%20Nalis/diary/408636), who had recently deployed the Panoramax instance of OSM Croacia, which took us to 99% of the way of deploying it (for the one exception see the details below).

And so we deployed our Panoramax instance to [***https://panoramax.libre.net.ar***](https://panoramax.libre.net.ar/).
As far as I know, this is not only the first instance in Latin America, but in all of the Americas![^2]
It currently is setup to accept images taken within Argentina and Uruguay, but if any folks in neighboring countries need some space, we can see what we can do, just get in touch with us!

Overall, this was a lot easier, less painful and even cheaper than I had expected.
And so we hope that this motivates others to give it a shot too.
Panoramax can only work at a larger scale, if more folks are standing up local instances. 
Maybe not even at a national level, but at a regional or municipal level, bringing down storage needs and thus cost.

In terms of admin effort and expertise needed:
There's a growing number of people who have Panoramax server admin experience, and in my experience everyone is super happy to help.
Having said that, having a small team of people willing to help is great, both to be able to get second opinions and help with the bus-factor - but also to turn it into a social effort! 
For our little instance, that includes not only Marcos and Dani, but also [Lucas](https://www.openstreetmap.org/user/lbellomo), who offered to share his server admin expertise as well when he heard of our idea!


## Appendix: Setting up the Software RAID

The only thing missing from the detailed write-up about the OSM-HR instance was on how to set-up the software RAID.
The server setup of OVH comes without any of the HDDs configured, as it just formats the SSD instead.
But luckily that was not too hard [to read up on](https://hetmanrecovery.com/recovery_news/how-to-create-software-raid-5-with-lvm.htm).
For others who want to reproduce this, the steps are below.

To get the 4 HDD setup as a RAID5 we went with LVM too, which is similar to the description for OSM-HR. 
To prepare the HDDs, we first created the physical volumes:

```
pvcreate /dev/sda
pvcreate /dev/sdb
pvcreate /dev/sdc
pvcreate /dev/sdd
```

Then we created the volume group `vg1` by running `vgcreate vg1 /dev/sda /dev/sdb /dev/sdc /dev/sdd`

Lastly, we created the actual LV: `lvcreate -n panoramax-photos --type raid5 -i 3 -l 100%VG vg1`

The parameters for this are 

```
    -n: name of volume
    --type raid5: raid type
    -i: use 3 devices out of the 4 (for single drive redundancy)
    -l 100%VG: use 100% of volume group
```

As a result we got:

```
root@panoramax-ar:~# lvscan
  ACTIVE            '/dev/vg1/panoramax-photos' [16.37 TiB] inherit
```

From there on, [the rest of the instructions of OSM HR](https://www.openstreetmap.org/user/Matija%20Nalis/diary/408636) will get you there!

## Footnotes

[^1]: To be clear, depending on your local context this of course can still be out of financial reach, but it has a lot lower start-up cost than many alternatives. And I'm lucky enough to be able to shoulder the cost, especially since [we shut down openSNP](https://tzovar.as/sunsetting-opensnp/) last year, which was the main monthly server cost I paid before. But if you want to help pay these bills, [feel free](https://ko-fi.com/gedankenstuecke).

[^2]: At least the first publicly visible and accessible instance that aims to federate, of course there might be privately hosted instances.

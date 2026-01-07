+++
title = "CD Collection"
date = "2025-04-14T21:47:00-06:00"
tags = ["music", "physical media", "ipod", "jellyfin"]
aliases = ["/blog/cd-collection"]
+++

I have been cultivating a growing collection of CDs. I don't have a convenient CD player, so I've been ripping them and putting their music on my modded iPod Classic, as well as my own Jellyfin media server. But why?

I've grown increasingly frustrated as streaming services decline in quality while also increasing their subscription prices. Not only that, but streaming services pay virtually nothing to artists. I decided to start collecting CDs and listening to music I enjoy on my iPod, free from algorithms and suggested content. Purchasing CDs is a legitimate way to financially support the artists as well (for artists that don't offer CDs, [Bandcamp](https://bandcamp.com/rubiefawn) gives artists a more reasonable cut).

I find that I now listen to full albums rather than cherry-picking individual songs like I would have on Spotify. Viewing albums as the primary unit of music as opposed to singles has changed the way I enjoy music (and I just think that's neat!)

Benn Jordan's [Why Spotify Will Ultimately Fail](https://youtu.be/gDfNRWsMRsU) and [Spotify's Phony War On Bots](https://youtu.be/kVY7-Ti77UQ) are excellent videos that explore the unsavory details behind the music streaming business model (not just Spotify). There is no better time to take control of your music; it sounds better without corporate greed.

## Converting FLAC to ALAC

The iPod Classic is not capable of playing FLAC audio files (at least, not without [Rockbox](https://www.rockbox.org)). In order to sync my FLAC CD rips to my iPod, I have to convert them to Apple's ALAC format. To do this, I use the all-powerful [ffmpeg](https://ffmpeg.org):

```sh
ffmpeg -i $INPUT_FLAC -map 0:a -acodec alac $OUTPUT_M4A
```

Since I'm typically converting an entire CD's worth of files at once, I use the following command:

```nu
ls **/*.flac
| insert out {|it| $it.name | str replace "flac" "m4a"}
| each {|it| ffmpeg -i $it.name -map 0:a -acodec alac $it.out}
```

The shell used here is [nushell](https://www.nushell.sh), but other shells can do the same thing ({{< highlight sh "hl_inline=true" >}}find -exec{{< /highlight >}} in POSIX, {{< highlight ps1 "hl_inline=true" >}}Get-Item -Recurse | ForEach-Object{{< /highlight >}} in Powershell, and so on). Or you could just install Rockbox on the iPod in order to play FLAC files directly, and save yourself the trouble of maintaining two copies of your music library, lmao.

It's worth noting that converting to ALAC seems to strip most of the metadata tags, which seems to be the result of some Apple nonsense [ffmpeg doesn't account for](https://trac.ffmpeg.org/ticket/2798). I've been using [mp3tag](https://www.mp3tag.de) to import metadata from [musicbrainz](https://musicbrainz.org) and then bulk copy/paste the tags from the FLAC to the ALAC files, but I'd prefer to only do this once through the command line (mp3tag is GUI-only). I've also heard that [atomicparsely](https://github.com/wez/atomicparsley) can help with this sort of thing, but I haven't investigated that yet. I'll update this once I figure out how to handle this.

Below is a list of all of the CDs I have collected so far, updated as needed. Happy listening!

- [ABBA — Arrival](https://musicbrainz.org/release-group/e464e167-83ab-3b59-88bd-262cf552056e)
- [Arctic Monkeys — AM](https://musicbrainz.org/release-group/a348ba2f-f8b3-4686-b928-e63d8d94d543)
- [Beach House — Bloom](https://musicbrainz.org/release-group/f1390eec-a192-4d96-9943-bbc42a819588)
- [Billie Eilish — WHEN WE ALL FALL ASLEEP, WHERE DO WE GO?](https://musicbrainz.org/release-group/72375978-a9a1-4254-b957-85565c716b7e)
- [Billie Eilish — HIT ME HARD AND SOFT](https://musicbrainz.org/release-group/02a544b3-0459-42c7-bd9c-047162e7b67a)
- [boygenius — the record](https://musicbrainz.org/release-group/1339f18d-220a-4405-8ae9-e52993b1ef70)
- [CAKE — Comfort Eagle](https://musicbrainz.org/release-group/cdb7197f-ed14-3238-a402-d0e554bcc0c8)
- [CAKE — Pressure Chief](https://musicbrainz.org/release-group/84bf56d1-4f0d-35b2-9195-fc4b44ddd6b2)
- [CAKE — Fashion Nugget](https://musicbrainz.org/release-group/3bd5a388-774f-3b47-b3f8-5b3463cbcb13)
- [Cavetown — Sleepyhead](https://musicbrainz.org/release-group/a780b6a1-7047-42c3-94c3-94e202f9f1c2)
- [Cavetown — Worm Food](https://musicbrainz.org/release-group/0278745d-eacc-4b7f-b9a9-0ad8c8e66667)
- [Chappell Roan — The Rise and Fall of a Midwest Princess](https://musicbrainz.org/release-group/eda0f5d3-44eb-42cf-bcfd-5c9f93e05c7a)
- [Charli xcx — brat](https://musicbrainz.org/release-group/e0fdb431-0109-420d-8a37-f99eaeb4d671)
- [Cigarettes After Sex — Cigarettes After Sex](https://musicbrainz.org/release-group/90a85020-515c-427e-9a59-5cc4dbcac0e3)
- [Cigarettes After Sex — Cry](https://musicbrainz.org/release-group/21759bba-54bc-4357-8f17-a6bcd50e0aa3)
- [CHVRCHES — The Bones of What You Believe (10 year anniversary special edition)](https://musicbrainz.org/release-group/b2cbaa83-cb77-4961-8ca3-816b24e5204f)
- [CHVRCHES — Love is Dead](https://musicbrainz.org/release-group/d4ae1ef3-2694-4cf9-be55-730818b60ba9)
- [Clairo — Charm](https://musicbrainz.org/release-group/6fb46378-9cb7-4693-b76d-a62f2cc09214)
- [Cocteau Twins — Heaven or Las Vegas](https://musicbrainz.org/release-group/12fab6b9-4eaf-33b0-963e-cae03ac332fe)
- [The Crane Wives — Coyote Stories](https://musicbrainz.org/release-group/252f9f0e-6f10-41fd-a3b0-5e4d294315fd)
- [The Crane Wives — Foxlore](https://musicbrainz.org/release-group/d67e8d09-093f-4593-bbf6-9d84eb83e349)
- [The Crane Wives — Beyond Beyond Beyond](https://musicbrainz.org/release-group/9d8fff94-9f7d-4a27-a44b-f4a86d2197b0)
- [Death Cab for Cutie — Plans](https://musicbrainz.org/release-group/bc7630eb-521a-3312-a281-adfb8c5aac7d)
- [Deftones — Koi no Yokan](https://musicbrainz.org/release-group/87338b87-34e2-4f11-8e2e-600636b6dbcb)
- [dodie — Human](https://musicbrainz.org/release-group/026e44c8-5e2f-4371-ba9a-ef618781d3bb)
- [Erasure — Chorus](https://musicbrainz.org/release-group/d4eebf86-736e-3363-ad05-e8b457083810)
- [Foster the People — Torches](https://musicbrainz.org/release-group/535748f7-5b3d-4a2a-8f9c-bd8baa587239)
- [Hozier — Unreal Unearth](https://musicbrainz.org/release-group/7f7d47f8-a97b-420b-b2f8-c3dfd6ca505c)
- [Hozier — Hozier](https://musicbrainz.org/release-group/06af18b0-1f97-4282-8a4d-af53ae8d9377)
- [Hozier — Wasteland, Baby!](https://musicbrainz.org/release-group/29cffbed-9ae1-4e75-be82-6058c7e9e9b4)
- [John Williams — Star Wars: The Force Awakens](https://musicbrainz.org/release-group/405fd3c5-0a45-456a-b853-6f734d3b57aa)
- [John Williams — Star Wars: The Last Jedi](https://musicbrainz.org/release/ea9f7125-f7aa-4231-84a3-97729a78e1ab)
- [Joss Stone — Mind Body & Soul](https://musicbrainz.org/release-group/aa47a96b-f119-3174-b5df-aee7d1bb481f)
- [Keane — Hopes and Fears](https://musicbrainz.org/release/904616d9-0eca-4313-8dee-800fe5d27b0b)
- [Magdalena Bay — Mercurial World](https://musicbrainz.org/release-group/fda72ebb-16e5-4a19-8be5-8e41906e3c86)
- [Magdalena Bay — Imaginal Disk](https://musicbrainz.org/release-group/9c710cb4-6b20-47d7-a31a-5adb064c6c6b)
- [Maroon 5 — Songs About Jane](https://musicbrainz.org/release-group/93d5a1d1-73c0-34d3-b7f1-49473c48face)
- [Mazzy Star — So Tonight That I Might See](https://musicbrainz.org/release-group/2863caa0-14e9-3016-83e4-4f0cdab55f4f)
- [Mitski — Laurel Hell](https://musicbrainz.org/release-group/f99a6b54-900b-4f17-bdcf-451639873b5d)
- [Mother Mother — Grief Chapter](https://musicbrainz.org/release-group/c50b7be6-b92a-4bd3-8752-d082da8f8358)
- [MUNA — About U](https://musicbrainz.org/release-group/9946c638-b2ce-451a-9ceb-674500b94181)
- [Noah Kahan — Stick Season](https://musicbrainz.org/release-group/14fc4370-9b90-411b-a02e-6c6d5a8799fc)
- [Owl City — Ocean Eyes](https://musicbrainz.org/release-group/6fa35df1-a08e-42f5-aeb2-ae590ba9fd6b)
- [Phoebe Bridgers — Punisher](https://musicbrainz.org/release-group/cf75fd5a-ca84-4371-94d7-27410360f06b)
- [Phoebe Bridgers — Stranger in the Alps](https://musicbrainz.org/release-group/0dac7ff9-b4d1-423e-bf6b-7d194abb0e14)
- [Porter Robinson — Worlds](https://musicbrainz.org/release-group/6df00711-624a-4960-9f15-39baf9332cbb)
- [Porter Robinson — Nurture](https://musicbrainz.org/release-group/e2cc165d-b935-4e7c-8728-77cf01ab21ed)
- [Porter Robinson — SMILE! :D](https://musicbrainz.org/release-group/87964290-66b9-4fd9-bea3-1bfe9de92862)
- [PVRIS — Evergreen](https://musicbrainz.org/release-group/5e9a199f-a742-47e0-866a-7f878deb354e)
- [Queen — Greatest Hits I, II & III: The Platinum Collection](https://musicbrainz.org/release-group/f61e762a-2799-312c-a121-9e4114d66c0d)
- [Shura — forevher](https://musicbrainz.org/release-group/849ca9a7-4129-462f-9c1c-20d934b7d936)
- [Sia — We Are Born](https://musicbrainz.org/release-group/8c96a577-2d35-404d-9d3f-c45813fea935)
- [Soccer Mommy — color theory](https://musicbrainz.org/release-group/22d131b0-4de5-4cd8-a07f-e30555769934)
- [Tame Impala — Currents](https://musicbrainz.org/release-group/08aa7a6c-3e43-4459-87b2-e47faf3a088a)
- [Troye Sivan — Blue Neighbourhood](https://musicbrainz.org/release-group/0f9accd1-44bd-4e4c-ba80-dd6a0c0a6ddf)
- [The xx — xx](https://musicbrainz.org/release-group/23355caf-a543-4b5f-80fe-449101868fc1) 

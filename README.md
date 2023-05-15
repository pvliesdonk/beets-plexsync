# beets-plexsync
A plugin for [beets](https://github.com/beetbox/beets) to sync with your Plex server.

## Installation

Install the plugin using `pip`:

```shell
pip install git+https://github.com/arsaboo/beets-plexsync.git
```

Then, [configure](#configuration) the plugin in your
[`config.yaml`](https://beets.readthedocs.io/en/latest/plugins/index.html) file.

## Configuration

Add `plexsync` to your list of enabled plugins.

```yaml
plugins: plexsync
```

Next, you can configure your Plex server and library like following (see instructions to obtain Plex token [here](https://support.plex.tv/articles/204059436-finding-an-authentication-token-x-plex-token/)).

```yaml
plex:
    host: '192.168.2.212'
    port: 32400
    token: PLEX_TOKEN
    library_name: 'Music'
```

If you want to import `spotify` playlists, you will also need to configure the `spotify` plugin. If you are already using the [Spotify](https://beets.readthedocs.io/en/stable/plugins/spotify.html) plugin, `plexsync`will reuse the same configuration.
```yaml
spotify:
    client_id: CLIENT_ID
    client_secret: CLIENT_SECRET
```

## Features

The following features are implemented in `plexsync`:

* `beet plexsync [-f]`: allows you to import all the data from your Plex library inside beets. Run the command `beet plexsync` and it will obtain `guid`, `ratingkey`, `userrating`, `skipcount`, `viewcount`, `lastviewedat`, `lastratedat`, and `plex_updated`. See details about these attributes [here][plaxapi]. By default, `plexsync` will not overwrite information for tracks that are already rated. If you want to overwrite all the details again, use the `-f` flag, i.e., `beet plexsync -f` will force update the entire library with fresh information from Plex. This can be useful if you have made significant changes to your Plex library (e.g., updated ratings).

* `beet plexsyncrecent`: If you have a large library, `beets plexsync -f` can take a long time. To update only the recently updated tracks, use `beet plexsyncrecent` to update the information for tracks listened in the last 7 days.

* `plexplaylistadd` and `plexplaylistremove` to add or remove tracks from Plex playlists. These commands should be used in conjunction with beets [queries][queries_] to provide the desired items. Use the `-m` flag to provide the playlist name to be used.

   ** To add all country music tracks with `plex_userrating` greater than 5 in a playlist `Country`, you can use the command `beet plexplaylistadd -m Country genre:"Country" plex_userrating:5..`

   ** To remove all tracks that are rated less than 5 from the `Country` playlist, use the command `beet plexplaylistremove -m Country plex_userrating:..5`

* `beet plexplaylistimport`: allows you to import playlists from other online services. Spotify, Apple Music, Gaana.com, JioSaavn are currently supported (more coming soon). Use the `-m` flag to specify the playlist name to be created in Plex and supply the full playlist url with the `-u` flag.

  For example, to import the Global Top-100 Apple Music playlist, use the command `beet plexplaylistimport -m Top-100 -u https://music.apple.com/us/playlist/top-100-global/pl.d25f5d1181894928af76c85c967f8f31`. Similarly, to import the Hot-hits USA playlist from Spotify, use the command `beet plexplaylistimport -m HotHitsUSA -u https://open.spotify.com/playlist/37i9dQZF1DX0kbJZpiYdZl`

* `beet plexplaylistclear`: allows you to clear a Plex playlist. Use the `-m` flag to specify the playlist name to be cleared in Plex.

* `beet plexcollage`: allows you to create a collage of most played albums. You can use the `-i` flag to specify the number of days to be used (default is 7 days) and `-g` flag to specify the grid size (default is 3). So, `beet plexcollage -g 5 -i 7` can be used to create a 5x5 collage of the most played albums over the last 7 days. You should get a collage.png file in the beet config folder. The output should look something like the following:

<p align="center">
  <img src="collage.png">
</p>

## Advanced
Plex matching may be less than perfect and it can miss tracks if the tags don't match perfectly. You can enable manual search to improve the matching by enabling `manual_search` in your config (default: `False`):

```yaml
plexsync:
    manual_search: yes
```


[collage]: collage.png
[queries_]: https://beets.readthedocs.io/en/latest/reference/query.html?highlight=queries
[plaxapi]: https://python-plexapi.readthedocs.io/en/latest/modules/audio.html
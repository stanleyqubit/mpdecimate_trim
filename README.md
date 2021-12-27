Script to trim similar/duplicate fragments from video clips. While keeping audio in sync.

Note that it does not do what is often called "dropping frames" (i.e. removing them from container by replacing with PTS of a "similar enough" one from another part of the clip). It actually gets rid of them completely, making the resulting clip shorter in time.

Note also that some variables, such as mpdecimate thresholds or output codec settings, are currently hardcoded in the script. Mostly due to laziness ;-).

# Usage

Needs `Python3.7+` and `ffmpeg`.

```bash
$ mpdecimate_trim.py [--skip SKIP] [--keep] [--vaapi <render_device_filepath>] [--vaapi-decimate [render_device_filepath]] <filepath>
```

This will take file at `<filepath>`, detect frames with certain similarity, re-encode it with them removed (using `libx265`/`hevc_vaapi`) and delete the original file.

The `--keep` switch makes it keep the original.

By default, re-encode happens even if no fragments to trim are found. This can be adjusted by setting `--skip` to minimum amount of remaining clip parts (e.g. `<=1` is equivalent to default, `2` means 1 trimmed fragment, and so on).

The `--vaapi` option enables [VA-API](https://trac.ffmpeg.org/wiki/Hardware/VAAPI) based hardware accelerated transcoding. Note that the script does not check whether supplied input and/or available GPU are capable of performing the transcode, if they are not the process will fail.

The `--vaapi-decimate` option enables VA-API based hardware accelerated decimate filter. If the optional device path is supplied, this device will be used. Otherwise, it will attempt to use device specified with `--vaapi` option. If neither device is specified, the script will fail. Not that on some older versions of `ffmpeg` this might fail even if VA-API transcoding works. I have only tested this with `>=4.4.1`.

# vs_decimate?

Was a different experiment, using `vapoursynth`. Abandoned, because its' decimation algorithm does not fit my needs, and the whole process is also noticeably slower.

In case you want to use it for something, it needs `Python3.6+` and `vapoursynth` with `ffms2` and `damb`.

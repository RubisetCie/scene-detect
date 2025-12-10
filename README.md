
![PySceneDetect](https://raw.githubusercontent.com/RubisetCie/PySceneDetect/main/website/pages/img/pyscenedetect_logo_small.png)
==========================================================
Video Cut Detection and Analysis Tool
----------------------------------------------------------

**Quick Start (Command Line)**:

Split input video on each fast cut using `ffmpeg`:

    scenedetect -i video.mp4 split-video

Save some frames from each cut:

    scenedetect -i video.mp4 save-images

Skip the first 10 seconds of the input video:

    scenedetect -i video.mp4 time -s 10s

More examples can be found throughout [the documentation](https://www.scenedetect.com/docs/latest/cli.html).

**Quick Start (Python API)**:

To get started, there is a high level function in the library that performs content-aware scene detection on a video (try it from a Python prompt):

```python
from scenedetect import detect, ContentDetector
scene_list = detect('my_video.mp4', ContentDetector())
```

`scene_list` will now be a list containing the start/end times of all scenes found in the video.  There also exists a two-pass version `AdaptiveDetector` which handles fast camera movement better, and `ThresholdDetector` for handling fade out/fade in events.

Try calling `print(scene_list)`, or iterating over each scene:

```python
from scenedetect import detect, ContentDetector
scene_list = detect('my_video.mp4', ContentDetector())
for i, scene in enumerate(scene_list):
    print('    Scene %2d: Start %s / Frame %d, End %s / Frame %d' % (
        i+1,
        scene[0].get_timecode(), scene[0].frame_num,
        scene[1].get_timecode(), scene[1].frame_num,))
```

We can also split the video into each scene if `ffmpeg` is installed (`mkvmerge` is also supported):

```python
from scenedetect import detect, ContentDetector, split_video_ffmpeg
scene_list = detect('my_video.mp4', ContentDetector())
split_video_ffmpeg('my_video.mp4', scene_list)
```

For more advanced usage, the API is highly configurable, and can easily integrate with any pipeline. This includes using different detection algorithms, splitting the input video, and much more. The following example shows how to implement a function similar to the above, but using [the `scenedetect` API](https://www.scenedetect.com/docs/latest/api.html):

```python
from scenedetect import open_video, SceneManager, split_video_ffmpeg
from scenedetect.detectors import ContentDetector
from scenedetect.video_splitter import split_video_ffmpeg

def split_video_into_scenes(video_path, threshold=27.0):
    # Open our video, create a scene manager, and add a detector.
    video = open_video(video_path)
    scene_manager = SceneManager()
    scene_manager.add_detector(
        ContentDetector(threshold=threshold))
    scene_manager.detect_scenes(video, show_progress=True)
    scene_list = scene_manager.get_scene_list()
    split_video_ffmpeg(video_path, scene_list, show_progress=True)
```

See [the documentation](https://www.scenedetect.com/docs/latest/api.html) for more examples.

**Benchmark**:

We evaluate the performance of different detectors in terms of accuracy and processing speed. See the [benchmark report](benchmark/README.md) for details.

## Reference

 - [Documentation](https://www.scenedetect.com/docs/) (covers application and Python API)
 - [CLI Example](https://www.scenedetect.com/cli/)
 - [Config File](https://www.scenedetect.com/docs/0.6.4/cli/config_file.html)

## License

BSD-3-Clause; see [`LICENSE`](LICENSE) and [`THIRD-PARTY.md`](THIRD-PARTY.md) for details.

----------------------------------------------------------

Copyright (C) 2014-2024 Brandon Castellano.
All rights reserved.

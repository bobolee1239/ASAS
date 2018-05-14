# [ASAS] madmom
An audio signal processing library in Python with strong focus on MIR tasks.

<img alt="組長 陳玟詮" src="https://scontent-tpe1-1.xx.fbcdn.net/v/t34.18173-12/26754357_1995601330457755_1492089480_n.jpg?_nc_cat=0&_nc_eui2=AeG1Iy1gTI-NW0G926Cagf_rl3l66f-sp0sUgE_A0K5XbSDG7WIiQT0T56O-hxkTj6ksA39Ym42HIakgCCvCya_Pl7EcwnHD_FawTR4TGTfyxQ&oh=2e42efd04cf0c99d748c27fa860738e5&oe=5AF7B7D8" width=150 height = 200 align=right border=2.5 >


__Advantages:__
<ul>
<li> easy to use </li>
<li> rapid prototyping of signal processing workflows.</li>
<li> most things are modeled as numpy arrays.</li>
<li> simple conversion of a workflow to a running program by the use of processors.</li>
<li> no dependencies on other software packages. </li>
<li> inclusion of reference implementations for several state-of-art algorithms.</li>
</ul>

 ---

## Installation
__Prerequisites:__
* numpy >= 1.8.1
* scipy >= 0.16
* cython >= 0.25
* nose
* pyaudio
* <a href="https://www.ffmpeg.org/download.html">ffmpeg</a> 
   ```console
   $ sudo apt-get install ffmpeg    # Ubuntu
   $ brew install ffmpeg            # macOS
   ```

__Pip Install:__
```console
$ pip install madmom
```

If you installed package locally, the programs will be copied to a folder which might not be included in your `$PATH`, It's convenient to modify `~/.bashrc` or `~/.bash_profile` file

```console
$ echo "export PATH=<path/to/scripts>:$PATH" >> ~/.bashrc
```

<b><a href="https://download.microsoft.com/download/5/f/7/5f7acaeb-8363-451f-9425-68a90f98b238/visualcppbuildtools_full.exe?fixForIE=.exe.">Visual Studio</a></b> for Windows users. (Big S/O to Jenna Kang)<br>
<b><a href="https://drive.google.com/file/d/1iCqNZyuOthbWGlp_YVFMUMiCV6Xwucyf/view">madmom .whl file</a></b> (Big S/O to Sissi Lee)

```console
$ pip install --upgrade pip
$ cd C:/directory/to/file
$ pip install package-to-install.whl
```


---

## HW1 by madmom
```Python
1 import madmom
2 import matplotlib.pyplot as plt
3 signal = madmom.audio.signal.Signal('Rolling in the deep_10s.wav')
4 framedSignal = madmom.audio.signal.FramedSignal(signal,
                                                  frame_size = 1024,
                                                  fps = 100)
5 stft = madmom.audio.stft.STFT(framedSignal)
6 spectrogram = madmom.audio.spectrogram.Spectrogram(stft)
7 plt.imshow(spectrogram.T, aspect='auto', origin='lower')
8 plt.show()
```
---

## Beat Tracking in One Minute

### RNNBeatProcessor Prototype
```python
class madmom.features.beats.RNNBeatProcessor(
           post_processor = <function average_predictions>,
           online = False,
           nn_files = None,
           **kwargs
      )
```
__Arguments:__
* __post_processor [Processor]__ (optional):<br> default is to average the predictions
* __online [Bool]__ (optional): <br> Set `True` for online processing.
* __nn_files [List]__ (optional) <br>List of RNN Models to use, if processing power is limited, you can assign fewer number of RNN models via this argument.

__Return:__
* 1d array represents the probability of a beat at each frame.

__Example__
```python
>>> from madmom.features.beats import RNNBeatProcessor
>>> from madmom.models import BEATS_LSTM
>>> processor = RNNBeatProcessor(nn_files = [BEAT_LSTM[0]])
<madmom.features.beats.RNNBeatProcessor object at 0x1269e55c7>
>>> processor('./song.wav', fps = 100)
array([0.037, 0.024, 0.003, ... 0.005], dtype=float32)
```

### DBNBeatTrackingProcessor Prototype
```python
class madmom.features.beats.DBNBeatTrackingProcessor(
           min_bpm = 55.0,
           max_bpm = 215.0,
           num_tempi = None,
           transition_lambda = 100,
           observation_lambda = 16,
           correct = True,
           threshold = 0,
           fps = None,
           online = False,
           **kwargs
      )
```
__Arguments:__
* __fps [Float]__ :<br> Frames per second.
* __min_bpm [Float]__ (optional):<br> Minimum tempo used for beat tracking
* __max_bpm [Float]__ (optional):<br> Maximum tempo used for beat tracking
* __num_tempi [Int]__ (optional):<br> Number of tempi to model.
* __online [Bool]__ (optional): <br> Use the forward algorithm (instead of VIterbi) to decode the beats


__Return:__
* 1d array represents the position of the beats in seconds.

__Example__
```python
>>> from madmom.features.beats import RNNBeatProcessor, DBNBeatTrackingProcessor
>>> ## Explicitly assign frames per second
>>> processor = DBNBeatTrackingProcessor(fps = 100)
>>> processor
<madmom.features.beats.DBNBeatTrackingProcessor object at 0x1019e35c0>
>>> activation = RNNBeatProcessor()('Rolling in the deep_10s.wav')
>>> processor(activation)   ## Beats in second
array([0.41, 0.98, 1.56, 2.12, 2.7 , 3.27, 3.84, 4.41, 4.99, 5.56, 6.13,
       6.7 , 7.27, 7.84, 8.42, 8.99, 9.56])
```

---

## Q n A
1. Why class instance callable ?
2. Why is it important to assign a correct <b>fps</b> while calling _DBNBeatTrackingProcessor_?
3. Who is the man in the photo ?  

---

## Reference
1. http://madmom.readthedocs.org
2. https://github.com/CPJKU/madmom

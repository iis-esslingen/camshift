# CamShift Dataset and Benchmark

This is the official GitHub repository of the CamShift dataset and benchmark from the paper:

> **Neural Rendering for Sensor Adaptation in 3D Object Detection** \
> Felix Embacher, David Holtz, Jonas Uhrig, Marius Cordts, Markus Enzweiler

## News

## CamShift Dataset

![CamShift](https://github.com/user-attachments/assets/ac4d490d-deca-40f8-ac24-c97921da50f2)

### Dataset and Project Page
Please visit our [CamShift Project Page](https://dmholtz.github.io/camshift/) for more information. \
You can download a preview of the CamShift dataset [here](https://drive.google.com/drive/folders/1DRzwuDdljY7RaJwTRgsGoHX_vir1ddYj?usp=sharing). \
For complete dataset access, please reach out to [camshift@gmx.de](mailto:camshift@gmx.de).

### Class Overview
<table>
  <tr>
    <th>Category</th>
    <th># Instances</th>
    <th>Ratio %</th>
    <th colspan="3"># Instances per Scene</th>
  </tr>
  <tr>
    <th></th>
    <th></th>
    <th></th>
    <th>Total</th>
    <th>Train</th>
    <th>Val</th>
  </tr>
  <tr>
    <td>Ambulance</td>
    <td>226</td>
    <td>0.9</td>
    <td>0.3</td>
    <td>0.3</td>
    <td>0.3</td>
  </tr>
  <tr>
    <td>Bicycle</td>
    <td>946</td>
    <td>3.7</td>
    <td>1.1</td>
    <td>1.1</td>
    <td>1.0</td>
  </tr>
  <tr>
    <td>Bus</td>
    <td>294</td>
    <td>1.1</td>
    <td>0.3</td>
    <td>0.3</td>
    <td>0.4</td>
  </tr>
  <tr>
    <td>Car</td>
    <td>15762</td>
    <td>60.9</td>
    <td>18.5</td>
    <td>20.0</td>
    <td>12.1</td>
  </tr>
  <tr>
    <td>Human</td>
    <td>6114</td>
    <td>23.6</td>
    <td>7.2</td>
    <td>7.0</td>
    <td>8.1</td>
  </tr>
  <tr>
    <td>Motorcycle</td>
    <td>1397</td>
    <td>5.4</td>
    <td>1.6</td>
    <td>1.7</td>
    <td>1.3</td>
  </tr>
  <tr>
    <td>Truck</td>
    <td>1143</td>
    <td>4.4</td>
    <td>1.3</td>
    <td>1.4</td>
    <td>1.0</td>
  </tr>
</table>

### Setup
The CamShift dataset is designed for plug-and-play compatibility with the nuScenes dataset.
Therefore, each dataset split (sim-SUV, sim-SUB, nerf-SUV, and nerf-SUB) can be used as a drop-in replacement at the nuScenes root ``./data/nuscenes``.

## Sensor Adaptation Benchmark
### Setup
1. Clone repository and navigate to the repository root
2. Initialize all submodules with ``git submodule update --init``
3. Build docker or venv based on the instructions of each submodule
4. Clone the custom nuscenes-devkit using HTTPS ``git clone -b camshift https://github.com/iis-esslingen/nuscenes-devkit.git`` or SSH ``git clone -b camshift git@github.com:iis-esslingen/nuscenes-devkit.git``
5. Navigate to ``nuscenes-devkit/setup`` and install the custom nuscenes-devkit with ``pip install --no-deps .``

### Training and Validation on CamShift and Reproducing the Paper Results
1. Symlink one of the four CamShift datasets (sim-suv, sim-sub, nerf-suv or nerf-sub) to ``src/mmdet_projects/<your_project>/data/nuscenes``, e.g. ``ln -s /data/camshift/sim-suv ./src/mmdet_projects/<your_project>/data/nuscenes``
2. If ``mmdet3d`` is not located inside ``src/mmdet_projects/<your_project>``, symlink it with ``ln -s <path_to_mmdetection> ./src/mmdet_projects/<your_project>``
3. Navigate to ``src/mmdet_projects/<your_project>``, prepare the data, start the training and start the testing with the respective commands of the following table.

<table>
  <tr>
    <th>Model</th>
    <th>Usage</th>
    <th>Command</th>
  </tr>
  <tr>
    <td rowspan="3" style="text-align:center;">DETR3D</td>
    <td>Prep</td>
    <td><code>python tools/create_data.py nuscenes --root-path ./data/nuscenes --out-dir ./data/nuscenes --extra-tag detr</code></td>
  </tr>
  <tr>
    <td>Train</td>
    <td><code>tools/dist_train.sh projects/configs/detr3d/detr3d_r50.py 8</code></td>
  </tr>
  <tr>
    <td>Val</td>
    <td><code>./tools/dist_test.sh projects/configs/detr3d/detr3d_r50.py &lt;checkpoint_file&gt; 1 --eval bbox</code></td>
  </tr>
  <tr>
    <td rowspan="3" style="text-align:center;">PETR</td>
    <td>Prep</td>
    <td><code>python tools/create_data.py nuscenes --root-path ./data/nuscenes --out-dir ./data/nuscenes --extra-tag petr</code></td>
  </tr>
  <tr>
    <td>Train</td>
    <td><code>tools/dist_train.sh projects/configs/petr/petr_r50.py 8</code></td>
  </tr>
  <tr>
    <td>Val</td>
    <td><code>./tools/dist_test.sh projects/configs/petr/petr_r50.py &lt;checkpoint_file&gt; 1 --eval bbox</code></td>
  </tr>
  <tr>
    <td rowspan="3" style="text-align:center;">StreamPETR</td>
    <td>Prep</td>
    <td><code>python tools/create_data_nusc.py --root-path ./data/nuscenes --out-dir ./data/nuscenes --extra-tag nuscenes2d --version v1.0</code></td>
  </tr>
  <tr>
    <td>Train</td>
    <td><code>./tools/dist_train.sh ./projects/configs/StreamPETR/stream_petr_r50.py 8</code></td>
  </tr>
  <tr>
    <td>Val</td>
    <td><code>./tools/dist_test.sh ./projects/configs/StreamPETR/stream_petr_r50.py &lt;checkpoint_file&gt; 1 --eval bbox</code></td>
  </tr>
  <tr>
    <td rowspan="3" style="text-align:center;">BEVDet</td>
    <td>Prep</td>
    <td><code>python tools/create_data_bevdet.py</code></td>
  </tr>
  <tr>
    <td>Train</td>
    <td><code>tools/dist_train.sh configs/bevdet/bevdet_r50.py 8</code></td>
  </tr>
  <tr>
    <td>Val</td>
    <td><code>./tools/dist_test.sh configs/bevdet/bevdet_r50.py &lt;checkpoint_file&gt; 1 --eval mAP</code></td>
  </tr>
  <tr>
    <td rowspan="3" style="text-align:center;">BEVFormer&#8209;S</td>
    <td>Prep</td>
    <td><code>python tools/create_data.py nuscenes --root-path ./data/nuscenes --out-dir ./data/nuscenes --extra-tag nuscenes --version v1.0 --canbus ./data/nuscenes</code></td>
  </tr>
  <tr>
    <td>Train</td>
    <td><code>./tools/dist_train.sh ./projects/configs/bevformer/bevformer_r50_static.py 8</code></td>
  </tr>
  <tr>
    <td>Val</td>
    <td><code>./tools/dist_test.sh ./projects/configs/bevformer/bevformer_r50_static.py &lt;checkpoint_file&gt; 1</code></td>
  </tr>
  <tr>
    <td rowspan="3" style="text-align:center;">BEVFormer</td>
    <td>Prep</td>
    <td><code>python tools/create_data.py nuscenes --root-path ./data/nuscenes --out-dir ./data/nuscenes --extra-tag nuscenes --version v1.0 --canbus ./data/nuscenes</code></td>
  </tr>
  <tr>
    <td>Train</td>
    <td><code>./tools/dist_train.sh ./projects/configs/bevformer/bevformer_r50.py 8</code></td>
  </tr>
  <tr>
    <td>Val</td>
    <td><code>./tools/dist_test.sh ./projects/configs/bevformer/bevformer_r50.py &lt;checkpoint_file&gt; 1</code></td>
  </tr>
</table>

### Paper Results
<table>
  <thead>
    <tr>
      <th colspan="2"></th>
      <th colspan="6" style="text-align:center;">mAP [%]</th>
    </tr>
    </thead>
    <tbody>
    <tr>
      <td style="border-right: 1px solid black; text-align:left;">Training</td>
      <td style="border-right: 1px solid black; text-align:left;">Validation</td>
      <td style="text-align:left;">DETR3D†</td>
      <td style="text-align:left;">PETR‡</td>
      <td style="text-align:left;">StreamPETR‡</td>
      <td style="text-align:left;">BEVDet‡</td>
      <td style="text-align:left;">BEVFormer&#8209;S†</td>
      <td style="text-align:left;">BEVFormer†</td>
    </tr>
    <tr>
      <td style="border-right: 1px solid black; text-align:left;">sim&#8209;SUV</td>
      <td style="border-right: 1px solid black; text-align:left;">sim&#8209;SUV</td>
      <td style="text-align:left;">51.6</td>
      <td style="text-align:left;">46.8</td>
      <td style="text-align:left;">57.5</td>
      <td style="text-align:left;">44.2</td>
      <td style="text-align:left;">59.5</td>
      <td style="text-align:left;">63.3</td>
    </tr>
    <tr>
      <td style="border-right: 1px solid black; text-align:left;"><b>sim&#8209;SUB</b></td>
      <td style="border-right: 1px solid black; text-align:left;"><b>sim&#8209;SUB</b></td>
      <td style="text-align:left;"><b>46.1</b></td>
      <td style="text-align:left;"><b>43.6</b></td>
      <td style="text-align:left;"><b>52.7</b></td>
      <td style="text-align:left;"><b>41.9</b></td>
      <td style="text-align:left;"><b>56.6</b></td>
      <td style="text-align:left;"><b>61.1</b></td>
    </tr>
    <tr>
      <td style="border-right: 1px solid black; text-align:left;">nerf&#8209;SUV</td>
      <td style="border-right: 1px solid black; text-align:left;">nerf&#8209;SUV</td>
      <td style="text-align:left;">48.1</td>
      <td style="text-align:left;">41.1</td>
      <td style="text-align:left;">54.3</td>
      <td style="text-align:left;">38.3</td>
      <td style="text-align:left;">54.8</td>
      <td style="text-align:left;">58.4</td>
    </tr>
    <tr>
      <td style="border-right: 1px solid black; text-align:left;">nerf&#8209;SUB</td>
      <td style="border-right: 1px solid black; text-align:left;">nerf&#8209;SUB</td>
      <td style="text-align:left;">44.0</td>
      <td style="text-align:left;">36.6</td>
      <td style="text-align:left;">48.3</td>
      <td style="text-align:left;">38.4</td>
      <td style="text-align:left;">49.3</td>
      <td style="text-align:left;">51.9</td>
    </tr>
    <tr>
      <td style="border-right: 1px solid black; text-align:left;"><b>sim&#8209;SUV</b></td>
      <td style="border-right: 1px solid black; text-align:left;"><b>sim&#8209;SUB</b></td>
      <td style="text-align:left;"><b>29.7&nbsp;(−16.4)</b></td>
      <td style="text-align:left;"><b>14.9&nbsp;(−28.7)</b></td>
      <td style="text-align:left;"><b>17.3&nbsp;(−35.4)</b></td>
      <td style="text-align:left;"><b>29.4&nbsp;(−12.5)</b></td>
      <td style="text-align:left;"><b>48.8&nbsp;(−7.8)</b></td>
      <td style="text-align:left;"><b>50.8&nbsp;(−10.3)</b></td>
    </tr>
    <tr>
      <td style="border-right: 1px solid black; text-align:left;">sim&#8209;SUB</td>
      <td style="border-right: 1px solid black; text-align:left;">sim&#8209;SUV</td>
      <td style="text-align:left;">32.0</td>
      <td style="text-align:left;">10.2</td>
      <td style="text-align:left;">18.3</td>
      <td style="text-align:left;">25.6</td>
      <td style="text-align:left;">55.1</td>
      <td style="text-align:left;">57.2</td>
    </tr>
    <tr>
      <td style="border-right: 1px solid black; text-align:left;">nerf&#8209;SUV</td>
      <td style="border-right: 1px solid black; text-align:left;">sim&#8209;SUV</td>
      <td style="text-align:left;">47.7</td>
      <td style="text-align:left;">41.2</td>
      <td style="text-align:left;">53.0</td>
      <td style="text-align:left;">24.6</td>
      <td style="text-align:left;">55.4</td>
      <td style="text-align:left;">58.4</td>
    </tr>
    <tr>
      <td style="border-right: 1px solid black; text-align:left;"><b>nerf&#8209;SUB</b></td>
      <td style="border-right: 1px solid black; text-align:left;"><b>sim&#8209;SUB</b></td>
      <td style="text-align:left;"><b>43.1&nbsp;(+13.4)</b></td>
      <td style="text-align:left;"><b>35.1&nbsp;(+20.2)</b></td>
      <td style="text-align:left;"><b>44.8&nbsp;(+27.5)</b></td>
      <td style="text-align:left;"><b>29.5&nbsp;(+0.1)</b></td>
      <td style="text-align:left;"><b>50.6&nbsp;(+1.8)</b></td>
      <td style="text-align:left;"><b>52.1&nbsp;(+1.3)</b></td>
    </tr>
  </tbody>
</table>
<p>† [1600 x 900] input resolution. ‡ [1408 x 512] input resolution.</p>

![radar_chart](https://github.com/user-attachments/assets/ebbbaa76-145d-4bcd-a771-1a7fe6abbae1)

## License
The CamShift dataset is licensed under the terms of the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)](https://creativecommons.org/licenses/by-nc-sa/4.0/) license.  
The repository code is licensed under the terms of the [MIT license](https://git.i.mercedes-benz.com/EMBACHF/camshift/blob/main/LICENSE).  
Please note that each submodule additionally follows its own license and has its own dependencies.

## Citation

If you use the CamShift dataset, please cite:

```bibtex
@inproceedings{embacher2025camshift,
  author={Embacher, Felix and Holtz, David and Uhrig, Jonas and Cordts, Marius and Enzweiler, Markus},
  booktitle={2025 IEEE Intelligent Vehicles Symposium (IV)},
  title={Neural Rendering for Sensor Adaptation in 3D Object Detection},
  year={2025},
  volume={},
  number={},
  pages={},
  doi={},
}
```

## Acknowledgement
We want to thank the authors behind [CARLA](https://github.com/carla-simulator/carla) for their simulator and [nuScenes](https://www.nuscenes.org/) for their leading autonomous driving dataset, which have been crucial to create our virtual CamShift dataset and conducting our sensor adaptation investigations.  
Special thanks to the team behind [MMDetection3D](https://github.com/open-mmlab/mmdetection3d) for providing a comprehensive framework for 3D object detection. We also want to thank the authors of [DETR3D](https://github.com/WangYueFt/detr3d), [PETR](https://github.com/megvii-research/PETR), [StreamPETR](https://github.com/exiawsh/StreamPETR), [BEVDet](https://github.com/HuangJunJie2017/BEVDet), and [BEVFormer](https://github.com/fundamentalvision/BEVFormer) for their pioneering work and their open-source implementations.
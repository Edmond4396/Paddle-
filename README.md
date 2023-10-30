# Paddle-版面识别作业
使用paddle进行训练，实现对Text Figure Equation Table四类标签的识别


1.数据处理，将voc标注格式转为coco标注格式，即使用json.py来将xml文件修改为json文件，并且划分适当的训练集和验证集。

2.根据[官方版面分析说明文件](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.6/ppstructure/layout/README_ch.md)来安装配置本地环境（这里一定要注意自己cuda的版本号，装错了就还要再麻烦一次）

3.在[官方版面分析说明文件](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.6/ppstructure/docs/models_list.md)下载`picodet_lcnet_x1_0_fgd_layout.pdparams`作为本次训练的预训练模型。

4.在`configs\picodet\legacy_model\application\layout_analysis`下打开`picodet_lcnet_x1_0_layout.yml`文件进行编辑，

将其中的`pretrain_weights`修改为`configs\pretrain_weights\models\picodet_lcnet_x1_0_fgd_layout.pdparams`，这一步将下载好的预训练模型文件放到`configs\pretrain_weights\models`目录下即可。weights修改为`output/picodet_lcnet_x1_0_layout/model_final`。

epoch后的数字决定训练的次数，实测训练到30多epoch后准确率上升的次数较低，波动也较小，所以我选择了35个epoch进行训练。num_classes为标签种类，这次训练一共是四类。

接下来是对训练要使用的数据集路径的修改。将TrainDataset下image_dir修改为训练集图片名称，anno_path改为训练集的json文件名称，dataset_dir后的目录改为数据集的根目录，同理修改EvalDataset。TestDataset中anno_path修改为验证集json文件的路径。

5.若训练时出现显存不足的问题，修改TrainReader中的batch_size，根据比例缩小，并且等比例缩小修改另一个配置文件中的base_lr，该文件一般为`configs\picodet\legacy_model\_base_\optimizer_100e.yml`。

6.准备完毕后进入PaddleDetection环境，用以下命令开启训练。
```
python tools/train.py -c configs/picodet/legacy_model/application/layout_analysis/picodet_lcnet_x1_0_layout.yml  --eval
```


7.训练完毕后可以在`PaddleDetection\output\picodet_lcnet_x1_0_layout`文件夹下看到训练好的模型，然后以下用命令来开启预测，结果会保存在`PaddleDetection\output_dir`中
```
python tools/infer.py -c configs/picodet/legacy_model/application/layout_analysis/picodet_lcnet_x1_0_layout.yml -o weights='output/picodet_lcnet_x1_0_layout/best_model.pdparams' --infer_img=docs/images/layout.jpg --output_dir=output_dir/ --draw_threshold=0.5

# 在draw_threshold=处可以更改显示多少自信度以上的标注
# --infer_img=处可以改为--infer_dir=就可以按文件夹来预测

```

# Image-Segmentation-CDD-CESM-Mammogram (Updated 2023/07/20)
<h2>
1 Image-Segmentation-CDD-CESM-Mammogram 
</h2>
<p>
This is an experimental project for Image Segmentation of Mammogram by using
 <a href="https://github.com/atlan-antillia/Tensorflow-Slightly-Flexible-UNet">Tensorflow-Slightly-Flexible-UNet</a> Model,
which is a typical classic Tensorflow2 UNet implementation <a href="./TensorflowUNet.py">TensorflowUNet.py</a> 
<p>
The image dataset used here has been taken from the following web site.
</p>
<pre>
Categorized Digital Database for Low energy and Subtracted Contrast Enhanced Spectral Mammography images (CDD-CESM)
https://wiki.cancerimagingarchive.net/pages/viewpage.action?pageId=109379611#109379611bcab02c187174a288dbcbf95d26179e8
</pre>
<pre>
Citations & Data Usage Policy
Users must abide by the TCIA Data Usage Policy and Restrictions. Attribution should include references to the following citations:

Data Citation
Khaled R., Helal M., Alfarghaly O., Mokhtar O., Elkorany A., El Kassas H., Fahmy A. Categorized Digital Database 
for Low energy and Subtracted Contrast Enhanced Spectral Mammography images [Dataset]. (2021) The Cancer Imaging
 Archive. DOI:  10.7937/29kw-ae92 

Publication Citation
Khaled, R., Helal, M., Alfarghaly, O., Mokhtar, O., Elkorany, A., El Kassas, H., & Fahmy, A. Categorized contrast
 enhanced mammography dataset for diagnostic and artificial intelligence research. (2022) Scientific Data, 
 Volume 9, Issue 1. DOI: 10.1038/s41597-022-01238-0

TCIA Citation
Clark K, Vendt B, Smith K, Freymann J, Kirby J, Koppel P, Moore S, Phillips S, Maffitt D, Pringle M, Tarbox L,
 Prior F. The Cancer Imaging Archive (TCIA): Maintaining and Operating a Public Information Repository, 
 Journal of Digital Imaging, Volume 26, Number 6, December, 2013, pp 1045-1057. DOI: 10.1007/s10278-013-9622-7
</pre>
<br>
<h2>
2. Install Image-Segmentation-CDD-CESM-Mammogram 
</h2>
Please clone Image-Segmentation-CDD-CESM-Mammogram.git in a folder <b>c:\google</b>.<br>
<pre>
>git clone https://github.com/sarah-antillia/Image-Segmentation-CDD-CESM-Mammogram.git<br>
</pre>
You can see the following folder structure in your working folder.<br>

<pre>
Image-Segmentation-CDD-CESM-Mammogram 
├─asset
└─projects
    └─Mammogram
        ├─eval
        ├─generator
        ├─Mammogram
        │  ├─test
        │  │  ├─images
        │  │  └─masks
        │  ├─train
        │  │  ├─images
        │  │  └─masks
        │  └─valid
        │      ├─images
        │      └─masks
        ├─mini_test_output
        ├─mini_test_output_merged
        └─models
</pre>

<h2>
3 Prepare dataset
</h2>

<h3>
3.1 Download master dataset
</h3>
  Please download the original image and assocated annotation and segmentation files from the following link.<br>
<pre>
Categorized Digital Database for Low energy and Subtracted Contrast Enhanced Spectral Mammography images (CDD-CESM)
https://wiki.cancerimagingarchive.net/pages/viewpage.action?pageId=109379611#109379611bcab02c187174a288dbcbf95d26179e8
</pre>
The dataset has the following folder structure.<br>
<pre>
CDD-CESM
├─Low energy images of CDD-CESM
└─Subtracted images of CDD-CESM
</pre>.
and csv and xlsx files<br>.
<pre>
CDD-CESM
├─Radiology_hand_drawn_segmentations_v2.csv
├─Radiology-manual-annotations.csv
└─Radiology-manual-annotations.xlsx
</pre>,
where Radiology-manual-annotations.csv is just a converted csv file from the original xlsx file by us.<br>
<pre>
 Radiology manual annotations.xlsx
</pre>
This csv file will be used to identify a category ("Benign", "Malgnant") of each image in CDD-CESM dataset.<br>
<h3>
3.2 Create Malignant image and mask dataset
</h3>
By using Python script <a href="./projects/Mammogram/generator/ImageMaskDatasetGenerator.py">ImageMaskDatasetGenerator.py</a>,
 we have created <b>Malignant CDD-CESM-master</b> dataset from the original image jpg files and mask data files
in the <b>CDD-CESM</b> folder.<br>
The script performs the following image processings.<br>
<h3>3.2.1 Resize Malignant images to 512x512</h3>
We have resized all "Malignant" image files to 512x512 in the following folders.
<pre>
  Low energy images of CDD-CESM
  Subtracted images of CDD-CESM
</pre>
We are able to identify a category("Benign, "Malignant") of each image in those 
folders by using <b>Radiology-manual-annotations.csv</b> file.<br>
For example, <b>P316_L_DM_CC</b> image is "Malignant" as shown below.<br>
<img src="./asset/Radiology-manual-annotations.csv.png"  width="720" height="auto">

<h3>3.2.2 Create 512x512 square mask files </h3>
We have created 512x512 square mask files corresponding to those "Malignant" images by using 
circles, ellipses and polygons json data defined in <b>Radiology_hand_drawn_segmentations_v2.csv</b>.<br>
For example, the malignant <b>P316_L_DM_CC.jpg</b> image has four segmented regions (two circles 
and two polygons) as shown below.<br>
<img src="./asset/Radiology_hand_drawn_segmentations_v2.csv.png" width="720" height="auto"><br>
<h3>3.2.3 Augment images and masks </h3>
We have created flipped and mirrored image and mask files to augment the 512x512 image and mask files.
<br>
<br>
<b>NOTE</b><br>
<a href="./projects/Mammogram/SomeImageIds_are_missing_in_Radiology manual annotations.xlsx.txt">
Some Image_names in <b>Radiology_hand_drawn_segmentations_v2.csv</b> are missing in 
<b>Radiology manual annotations.xlsx</b> file.</a>
<br>
<br>
<h3>3.2.4 CDD-CESM-master </h3>

The created <b>CDD-CESM-master</b> dataset has the following folder structure.<br>
<pre>
CDD-CESM-master
└─malignant
    ├─images
    └─masks
</pre>

<h3>
3.3 Split master to test, train and valid 
</h3>
By using Python script <a href="./projects/Mammogram/generator/split_master.py">split_master.py</a>,
 we have finally created <b>Mammogram</b> dataset from the CDD-CESM-master.<br>
<pre>
├─test
│  ├─images
│  └─masks
├─train
│  ├─images
│  └─masks
└─valid
    ├─images
    └─masks
</pre>
<b>train/images samples:</b><br>
<img src="./asset/train_images_samples.png" width="1024" height="auto">
<br>
<b>train/masks samples:</b><br>
<img src="./asset/train_masks_samples.png"  width="1024" height="auto">
<br>

<h2>
4 Train TensorflowUNet Model
</h2>
 We have trained Mammogram TensorflowUNet Model by using the following
 <b>train_eval_infer.config</b> file. <br>
Please move to ./projects/Mammogram directory and run the following bat file.<br>
<pre>
>1.train.bat
</pre>
, which simply runs the following command.<br>
<pre>
>python ../../TensorflowUNetTrainer.py train_eval_infer.config
</pre>
<pre>
; train_eval_infer.config
; for Mammogram TensorflowUNet Model
; 2023/07/18 antillia.com

[model]
image_width    = 512
image_height   = 512

image_channels = 3
num_classes    = 1
base_filters   = 16
base_kernels   = (7,7)
num_layers     = 7
dropout_rate   = 0.06
learning_rate  = 0.0001
clipvalue      = 0.5
dilation       = (2,2)
loss           = "bce_iou_loss"
metrics        = ["iou_coef"]
show_summary   = False

[train]
epochs        = 100
batch_size    = 2
patience      = 10
metrics       = ["iou_coef", "val_iou_coef"]
model_dir     = "./models"
eval_dir      = "./eval"
image_datapath = "./Mammogram/train/images"
mask_datapath  = "./Mammogram/train/masks"
create_backup = False

[eval]
image_datapath = "./Mammogram/valid/images"
mask_datapath  = "./Mammogram/valid/masks"
output_dir     = "./eval_output"

[infer] 
images_dir = ".//Mammogram/test/images"
output_dir = "./mini_test_output"
merged_dir = "./mini_test_output_merged"

[mask]
blur      = True
binarize  = True
threshold = 74
</pre>

Please note that image size setting of the config file to be the same size of our Mammogram image dataset.<br>
<pre>
[model]
image_width    = 512
image_height   = 512
</pre>

The training process has just been stopped at epoch 40 by an early-stopping callback as shown below.<br><br>
<img src="./asset/train_console_at_epoch_40_0718.png" width="720" height="auto"><br>
<br>
<br>
<b>Train metrics line graph</b>:<br>
<img src="./asset/train_metrics.png" width="720" height="auto"><br>
<br>
<b>Train losses line graph</b>:<br>
<img src="./asset/train_losses.png" width="720" height="auto"><br>

<!--
<h2>
5 Evaluation
</h2>
 We have evaluated prediction accuracy of our Pretrained Mammogram Model by using <b>valid</b> dataset.
Please move to ./projects/Mammogram/ and run the following bat file.<br>
<pre>
>2.evalute.bat
</pre>
, which simply run the following command.<br>
<pre>
>python ../../TensorflowUNetEvaluator.py ./train_eval_infer.config
</pre>
The evaluation result of this time is the following.<br>
<img src="./asset/evaluate_console_output_at_epoch_35_0607.png" width="720" height="auto"><br>
<br>
-->
<h2>
5 Inference 
</h2>
We have also tried to infer the segmented region for <pre>
images_dir    = "./projects/Mammogram/Mammogram/test/images" 
</pre> dataset defined in <b>train_eval_infer.config</b>,
 by using our Pretrained Mammogram UNet Model.<br>
Please move to ./projects/Mammogram directory, and run the following bat file.<br>

<pre>
>3.infer.bat
</pre>
, which simply runs the following command.<br>
Please move to ./projects/Mammogram/ and run the following bat file.<br>
<pre>
>python ../../TensorflowUNetInferencer.py ./train_eval_infer.config
</pre>

<b><a href="./projects/Mammogram/Mammogram/test/images">Test input images</a> </b><br>
<img src="./asset/test_image.png" width="1024" height="auto"><br>
<br>
<b><a href="./projects/Mammogram/Mammogram/test/masks">Test input ground truth mask</a> </b><br>
<img src="./asset/test_ground_truth.png" width="1024" height="auto"><br>
<br>

<b><a href="./projects/Mammogram/mini_test_output/">Inferred images </a>Test output</b><br>
<img src="./asset/inferred_test_output.png" width="1024" height="auto"><br>
<br>
<br>

<b><a href="./projects/Mammogram/Mammogram/test/images">Enlarged input images</a> </b><br>
<img src="./asset/enlarged_test_image.png" width="1024" height="auto"><br>
<br>

<b><a href="./projects/Mammogram/mini_test_output_merged">Enlarged merged inferred images</a></b><br>
<img src="./asset/inferred_test_output_merged.png" width="1024" height="auto"><br><br>

<br>
<!--
-->


<h3>
References
</h3>
<b>1. Categorized Digital Database for Low energy and Subtracted Contrast Enhanced Spectral Mammography images (CDD-CESM)
</b><br>
<pre>
https://wiki.cancerimagingarchive.net/pages/viewpage.action?pageId=109379611#109379611bcab02c187174a288dbcbf95d26179e8
</pre>
<pre>
Citations & Data Usage Policy
Users must abide by the TCIA Data Usage Policy and Restrictions. Attribution should include references to the following citations:

Data Citation
Khaled R., Helal M., Alfarghaly O., Mokhtar O., Elkorany A., El Kassas H., Fahmy A. Categorized Digital Database 
for Low energy and Subtracted Contrast Enhanced Spectral Mammography images [Dataset]. (2021) The Cancer Imaging
 Archive. DOI:  10.7937/29kw-ae92 

Publication Citation
Khaled, R., Helal, M., Alfarghaly, O., Mokhtar, O., Elkorany, A., El Kassas, H., & Fahmy, A. Categorized contrast
 enhanced mammography dataset for diagnostic and artificial intelligence research. (2022) Scientific Data, 
 Volume 9, Issue 1. DOI: 10.1038/s41597-022-01238-0

TCIA Citation
Clark K, Vendt B, Smith K, Freymann J, Kirby J, Koppel P, Moore S, Phillips S, Maffitt D, Pringle M, Tarbox L,
 Prior F. The Cancer Imaging Archive (TCIA): Maintaining and Operating a Public Information Repository, 
 Journal of Digital Imaging, Volume 26, Number 6, December, 2013, pp 1045-1057. DOI: 10.1007/s10278-013-9622-7
</pre>

<b>2. Categorized contrast enhanced mammography dataset for diagnostic and artificial intelligence research
</b><br>
Rana Khaled, Maha Helal, Omar Alfarghaly, Omnia Mokhtar, Abeer Elkorany,<br>
Hebatalla El Kassas & Aly Fahmy<br>
<pre>
https://www.nature.com/articles/s41597-022-01238-0
</pre>

<b>3. CDD-CESM-Dataset
</b><br>
<pre>
https://github.com/omar-mohamed/CDD-CESM-Dataset
</pre>

<b>4. Breast Cancer Segmentation Methods: Current Status and Future Potentials</b><br>
Epimack Michael, He Ma, Hong Li, Frank Kulwa, and Jing<br>
<pre>
https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8321730/
</pre>

<h3>Appendix</h3>
<b>Mammogram UNet Model Inspection</b><br>
Please move to ./projects/Mammogram directory, and run the following bat file.<br>
<pre>
>model_inspect.bat
</pre>

1. TensorflowUNet Model graph<br>
<img src="./projects/Mammogram/model.png"><br>
<br>
2. Model summary<br>
<pre>
<a href="./projects/Mammogram/summary.txt">summary.txt</a><br>

# SFF_MagSeq_MViTs
## Solar flare forecasting based on magnetogram sequences learning with multiscale vision transformers and data augmentation techniques

### Authors
Luís Fernando L. Grim and André Leon S. Gradvohl


Our codes employed Pytorch, a framework for training Deep Learning models with GPU support and automatic back-propagation, to load the MViTv2 s models with Kinetics-400 weights. To simplify the code implementation, eliminating the need for an explicit loop to train and the automation of some hyper-parameters, we use the PyTorch Lightning module. The inputs were batches of 10 samples with 16 sequenced images in 3-channel resized to 224 × 224 pixels and normalized from 0 to 1.

Most of the papers in our literature survey split the original dataset chronologically. Some authors also apply k-fold cross-validation to emphasize the evaluation of themodel stability. However, we adopt a hybrid split taking the first 50,000 to apply the 5-fold cross-validation between the training and validation sets (known data), with 40,000 samples for training and 10,000 for validation. Thus, we can evaluate performance and stability by analyzing the mean and standard deviation of all trained models in the test set, composed of the last 9,834 samples, preserving the chronological order (simulating unknown data).

We develop three distinct models to evaluate the impact of oversampling magnetogram sequences through the dataset. The first model, Solar Flare MViT (SF MViT), has trained only with the original data from our base dataset without using oversampling. In the second model, Solar Flare MViT over Train (SF MViT oT), we only apply oversampling on training data, maintaining the original validation dataset. In the third model, Solar Flare MViT over Train and Validation (SF MViT oTV), we apply oversampling in both training and validation sets.

We also trained a model oversampling the entire dataset. We called it the "SF_MViT_oTV Test" to verify how resampling or adopting a test set with unreal data may bias the results positively.



### Zenodo version

The Zenodo version is available at https://doi.org/10.5281/zenodo.10246577 and contains all files from the project including the checkpoint and the output files generated by the codes.



### Folders Structure

In the Root directory of the project, we have the folder Seq_Magnetogram which contains the references for source images with the corresponding labels in the next 24 h. and 48 h. in the respectively M24 and M48 sub-folders.

- M24/M48: both present the following sub-folders structure:
    SF_MViT:
    SF_MViT_oT:
    SF_MViT_oTV:
    SF_MViT_oTV_Test:

There is also two files in root:

- inst_packages.sh: install the packages and dependencies to run the models.
- download_MViTS.py: download the pre-trained MViTv2_S from pytorch and store it in the cache.

M24 and M48 folders hold reference text files (flare_Mclass...) linking the images in the magnetogram_jpg folders or the sequences (Seq16_flare_Mclass...)  in the Seqs16 folders with their respective labels. They also hold "cria_seqs.py" which was responsible for creating the sequences and "test_pandas.py" to verify head info and check the number of samples categorized by the label of the text files. All the text files with the prefix "Seq16" and inside the Seqs16 folder were created by "criaseqs.py" code based on the correspondent "flare_Mclass" prefixed text files.

Seqs16 folder holds reference text files, in which each file contains a sequence of images that was pointed to the magnetogram_jpg folders.

All SF_MViT... folders hold the model training codes itself (SF_MViT...py) and the corresponding job submission (jobMViT...), temporary input (Seq16_flare...), output (saida_MVIT... and MViT_S...), error (err_MViT...) and checkpoint files (sample-FLARE...ckpt). Executed model training codes generate output, error, and checkpoint files. There is also a folder called "lightning_logs" that stores logs of trained models.


### Naming pattern for the files:

magnetogram_jpg: follows the format "hmi.sharp_720s.<SHARP-ID>.<date>.magnetogram.fits.jpg" and
Seqs16: follows the format "hmi.sharp_720s.<SHARP-ID>.<init-date>.to.<end-date>", where:
- hmi is the instrument that captured the image
- sharp_720s is the database source of SDO/HMI.
- <SHARP-ID> is the identification of SHARP region, and can contain one or more solar ARs classified by the (NOAA).
- <date> is the date-time the instrument captured the image in the format yyyymmdd_hhnnss_TAI (y:year, m:month, d:day, h:hours, n:minutes, s:seconds).
- <init-date> is the date-time when the sequence starts and follow the same format of <date>.
- <end-date> is the date-time when the sequence ends and follow the same format of <date>.


Reference text files in M24 and M48 or inside SF_MViT... folders follows the format "<prefix>flare_Mclass_<forecasting-horizon>_<dataset>.txt<over>", where:
- <prefix>: is Seq16 if refers to a sequence or void if refers direct to images.
- <forecasting-horizon>: "24h" or "48h".
- <dataset>: is "TrainVal<n>" or "Test". The <n> refers to the split of Train/Val.
- <over>: void or "_over" after the extension (...txt_over): means temporary input reference that was over-sampled by a training model.


All SF_MViT...folders:

Model training codes: "SF_MViT_<oversampling-type>_M+_<forecasting-horizon>_<split-type><gpu-type>", where:
- <oversampling -type>: void or "oT" (over Train) or "oTV" (over Train and Val) or "oTV_Test" (over Train, Val and Test);
- <forecasting-horizon>: "24h" or "48h";
- <split-type>: "oneSplit" for a specific split or "allSplits" if run all splits.
- <gpu-type>: void is default to run 1 GPU or "2gpu" to run into 2 gpus systems;

Job submission files: "jobMViT_<queue>", where:
- <queue>: point the queue in Lovelace environment hosted on CENAPAD-SP (https://www.cenapad.unicamp.br/parque/jobsLovelace)

Temporary inputs: "Seq16_flare_Mclass_<forecasting-horizon>_<dataset>.txt<over>:
- <dataset>: train or val;
- <over>: void or "_over" after the extension (...txt_over): means temporary input reference that was over-sampled by a training model.

Outputs: "saida_MViT_Adam_10-7<split>", where:
- <split>: k0 to k4, means the correlated split of the output or void if the output is from all splits.

Error files: "err_MViT_Adam_10-7<split>", where:
- <split>: k0 to k4, means the correlated split of the error log file or void if the error file is from all splits.

Checkpoint files: "sample-FLARE_MViT_S_10-7-epoch=<n-epoch>-valid_loss=<loss-value>-Wloss_k=<n-split>.ckpt", where:
- <n-opoch>: epoch number of the checkpoint;
- <loss-value>: corresponding valid loss;
- <n-split>: 0 to 4.

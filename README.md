# Robust Drone Detection and Classification Using Convolutional Neural Networks in Low SNR Environments
Code to reproduce the experiments from the paper:

S. Glüge, M. Nyfeler, A. Aghaebrahimian, N. Ramagnano and C. Schüpbach, "Robust Low-Cost Drone Detection and Classification Using Convolutional Neural Networks in Low SNR Environments," in IEEE Journal of Radio Frequency Identification, vol. 8, pp. 821-830, 2024, doi: [10.1109/JRFID.2024.3487303](https://doi.org/10.1109/JRFID.2024.3487303)


## Dataset
The dataset is available at [kaggle](https://www.kaggle.com/datasets/sgluege/noisy-drone-rf-signal-classification-v2). Download the dataset and place it in a subfolder `data/`. 

It comes in the form of 3 filetypes:
- `class_stats.csv`: a single file containing the number of samples per class
- `SNR_stats.csv`:  a single file containing the number of samples per SNR
- `IQdata_sampleX_targetY_snrZ.pt`: sample files that contain the IQ signal of sample X, with target Y at SNR level of Z

## Load and inspect the dataset
Use the script `load_dataset.py` to load the dataset using a custom torch Dataloader. It also plots a sample of the dataset which should look like this: 
![sample_input_data.png](doc/img/Noise_snr22.png)
![sample_input_data.png](doc/img/DJI_snr30.png)
![sample_input_data.png](doc/img/Taranis_snr-4.png)

Note that we plot the power spectrum of the IQ signal, which is the log10 of the absolute value of the FFT of the IQ signal 
```python
power_spec = np.log10(np.sqrt(spectrogram_2d[0,:,:]**2 + spectrogram_2d[1,:,:]**2))
```

## Train a CNN (VGG) on the dataset
`train_model_cv5.py` trains a VGG11_BN on the dataset using 5-fold cross-validation. The script saves the model and the training history in a subfolder `results/experiments/experiment_name`. I.e. training a VGG11_BN for 50 epochs with start learning rate 0.005 and batch size 8 ->
`results/experiments/vgg11_bn_CV5_epochs50_lr0.005_batchsize8`

To train the model, run the following command:
```bash
python train_model_cv5.py
```

Training one epoch of the dataset takes about 5 minutes for a VGG11_BN on a single NVIDIA A100.

Results for each fold are stored as a dictionary in a pickle file (`results_foldN.pkl`). The dictionary contains the following keys:
- `train_weighted_acc`: weighted accuracy on the training set
- `train_acc`: accuracy on the training set
- `train_loss`: loss on the training set
- `val_weighted_acc`: weighted accuracy on the validation set
- `val_acc`: accuracy on the validation set
- `val_loss`: loss on the validation set
- `best_epoch`: epoch with the best validation accuracy
- `test_acc`: accuracy on the test set
- `test_weighted_acc`: weighted accuracy on the test set
- `test_predictions`: predictions on the test set
- `test_targets`: targets on the test set
- `test_snrs`: SNRs on the test set
- `class_names`: class names
- `train_idx`: indices of the training set
- `val_idx`: indices of the validation set
- `test_idx`: indices of the test set

The respective model is stored in a separate file `model_foldN.pth`. Furhtermore, the training/validation loss, accuracy and weighted accuracy are plotted and stored in a file, e.g. `plots/results_foldloss_foldN.png`.

## Evaluate the model


## Related Literature
S. Glüge, M. Nyfeler, A. Aghaebrahimian, N. Ramagnano and C. Schüpbach, "Robust Low-Cost Drone Detection and Classification Using Convolutional Neural Networks in Low SNR Environments," in IEEE Journal of Radio Frequency Identification, vol. 8, pp. 821-830, 2024, doi: [10.1109/JRFID.2024.3487303](https://doi.org/10.1109/JRFID.2024.3487303)

```
@ARTICLE{10737118,
  author={Glüge, Stefan and Nyfeler, Matthias and Aghaebrahimian, Ahmad and Ramagnano, Nicola and Schüpbach, Christof},
  journal={IEEE Journal of Radio Frequency Identification}, 
  title={Robust Low-Cost Drone Detection and Classification Using Convolutional Neural Networks in Low SNR Environments}, 
  year={2024},
  volume={8},
  number={},
  pages={821-830},
  doi={10.1109/JRFID.2024.3487303}
}
```
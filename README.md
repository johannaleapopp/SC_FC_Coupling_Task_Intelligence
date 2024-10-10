# SC_FC_Coupling_Task_Intelligence
 
## 1. Scope

This repository contains scripts that were used to conduct the analyses in **"Structural-Functional Brain Network Coupling During Task Performance Reveals Intelligence-Relevant Communication Strategies"** coauthored by Johanna L. Popp, Jonas A. Thiele, Joshua Faskowitz, Caio Seguin, Olaf Sporns and Kirsten Hilger (doi: will be updated after publication). In brief, we investigated the relationship between general intelligence and structural-functional brain network coupling (SC-FC coupling) operationalized with similarity and network communication measures during resting-state and seven different task conditions. The scripts found in this repository can be used to replicate all analyses or more generally, to study the association between intrinsic and task-induced SC-FC coupling and individual differences (e.g., in general intelligence). In case you have questions or trouble with running the scripts, feel free to reach out under [johanna.popp@uni-wuerzburg.de].

## 2. Data 

For the main sample analysis, data from the S1200 sample of the Human Connectome Project funded by the National Institute of Health were used (HCP; Van Essen et al., 2013).  For the replication of results, we used data from the PIOP1 and PIOP2 samples collected as a part of the Amsterdam Open MRI Collection (AOMIC; Snoek et al., 2021). All data analyzed in the current study can be accessed online:

**HCP**: [https://www.humanconnectome.org/study/hcp-young-adult/data-releases/]
**AOMIC PIOP1**: [https://openneuro.org/datasets/ds002785]
**AOMIC PIOP2**: [https://openneuro.org/datasets/ds002790]

## 3. Preprocessing 

To conduct main analyses in the HCP sample, the minimally preprocessed resting-state fMRI data from the HCP (Glasser et al., 2013) were used. As additional denoising strategy, nuisance regression as explained in Parkes et al. (2018; strategy no.6) with 24 head motion parameters, eight mean signals from white matter and cerebrospinal fluid and four global signals was applied. For task-based data, basis-set task regressors in addition to the other nuisance regressors were used to remove mean task-evoked activations (Cole et al., 2019). Most of the fMRI preprocessing steps were conducted externally, and code can be found here: [https://github.com/faskowit/app-fmri-2-mat]. To assess individual structural connectivity, the minimally preprocessed DWI data provided by the HCP were used and we ran the MRtrix pipeline for DWI processing (Civier et al., 2019; Tournier et al., 2019; [https://github.com/civier/HCP-dMRI-connectome]). Probabilistic streamline tractography was employed (Smith et al., 2012) and only streamlines fitting the estimated white matter orientations from the diffusion image were kept. For the replication analysis, data from the AOMIC PIOP1 and PIOP2 samples was downloaded in minimally preprocessed form (using fMRIPrep version 1.4.1.; Esteban et al., 2019) and further processed similarly as in the main sample. Brain networks were constructed using a multimodal parcellation dividing the brain into 360 nodes (Glasser et al., 2016). 

## 4. Computation of latent *g*-factor

General intelligence was operationalized as latent *g*-factor from 12 cognitive measures (Thiele et al., 2022). This *g*-factor was calculated using simplified bi-factor analyses as outlined in Dubois et al. (2018). Code for the computation of those *g*-factors can be found here: [https://github.com/jonasAthiele/BrainReconfiguration_Intelligence].

## 5. Structure and Script Description 

### 5.1. HCP Data Prep 
For the preparation of data from the HCP sample, the scripts should be run in the following order:   

1.	`HCP_MRI_data_import`: Import of all MRI data from folder structure on local machine (SC matrices, resting-state fMRI and task fMRI time courses).
2.	`HCP_prepare_behavioral_data`: Import and preparation of HCP behavioral data (Output: HCP_behavioral_personality_gscore).
3.	`HCP_prepare_SC_data_with_subcortical`: Preparation of structural connectivity matrices and creation of cell that is used for further analyses.
4.	`HCP_prepare_FC_resting_state_data_with_subcortical`: Preparation of resting-state functional connectivity matrices and creation of a cell that is used for further analyses. Included is: a) import of subject ID’s and time course data for all four runs (save in cell) b) exclusion of subjects that don’t have all four scans completed c) matching up node order according to node order of SC matrices d) computation of functional connectivity matrices from time courses e) averaging across all FC matrices for each subject and f) Fisher-z transformation of individual mean connectivity matrices.

*Motion Correction*
5.	`HCP_motion_data_import_resting_state`: Import of data for motion correction for 4 resting-state fMRI scans and creation of respective table.
6.	`HCP_motion_correction_resting_state`: This script is used for motion correction with data from framewise displacement (FD). It includes a) definition of resting-state scans that need to be excluded b) computation of mean FD values across the remaining scans that are used for confound regression. Lastly, FC matrices are excluded based on the motion criteria and ultimately saved as final FC matrices and mean FD values in a table used for further analyses.
7.	`HCP_motion_data_import_task_x`: There is one script for each task condition: Import of data for motion correction for 2 task-based fMRI scans and creation of respective table.
8.	`HCP_motion_data_correction_task_x`: There is one script for each task condition used for motion correction with data from framewise displacement (FD). It includes a) definition of task-based scans that need to be excluded b) computation of mean FD values across the remaining scans that are used for confound regression. Lastly, FC matrices are excluded based on the motion criteria and ultimately saved as final FC matrices and mean FD values in a table used for further analyses.

9. `HCP_find_subjects_with_complete_data`: Merging of all tables (behavioral data; SC matrices and FC matrices) and creation of final tables for subjects that have all data.

*Compute Coupling*
10. `HCP_compute_coupling_measures_resting_state`: This script is based on a source script from Zamani Esfahlani et al. (2022) and was adjusted accordingly. It performs the computation of communication and similarity matrices based on SC connectivity matrices and calculates coupling measure specific coupling values by correlating regional connectivity profiles of the communication/similarity matrix with the respective FC matrix.
11. `HCP_compute_coupling_measures_task_x`: This script is based on a source script from Zamani Esfahlani et al. (2022) and was adjusted accordingly. It calculates coupling measure-specific coupling values by correlating regional connectivity profiles of the communication/similarity matrix (computed with `HCP_compute_coupling_measures_resting_state`) with the respective FC matrix.

12. `HCP_split_lockbox_sample`: This script is used to partition the main sample (HCP) into a primary main sample (70%) and a lockbox sample (30%) to be able to conduct an initial replication. It takes into account the family structure in the HCP and makes sure that all families are in the same sample to keep them truly independent from one another.

### 5.2. AOMIC Data Prep 
For the preparation of data from the AOMIC PIOP1 and AOMIC PIOP2 samples, the scripts should be run in the following order: 

1.	`AOMIC_data_import`: Import of behavioral and MRI data from the AOMIC PIOP1 and PIOP2 samples.
2.	`AOMIC_PIOP1_PIOP2_motion_correction_with_FD`: This script is used for motion correction in the AOMIC PIOP1 and AOMIC PIOP2 sample by using information from framewise displacement.
3.	`AOMIC_PIOP1_PIOP2_prepare_FC_matrices`: Preparation of functional connectivity matrices and creation of a cell that is used for further analyses. Included is: a) computation of functional connectivity matrix from time courses and b) Fisher-z transformation of the individual mean connectivity matrices.
4.	`AOMIC_PIOP1_PIOP2_find_subjects_with_complete_data`: Detection of subjects with complete datasets (behavioral data, SC matrices, FC matrices and FD data) and creation of final tables used for further analyses. Please note that this script is divided into two parts: Part 1 needs to be run before ‘Compute Coupling’ folder and part 2 after ‘Compute Coupling’ folder.

*Compute Coupling*

*AOMIC PIOP1*
5.	`AOMIC_PIOP1_compute_coupling_measures_resting_state`: This script is based on a source script from Zamani Esfahlani et al. (2022) and was adjusted accordingly. It performs the computation of communication and similarity matrices based on SC connectivity matrices and calculates coupling measure-specific coupling values by correlating regional connectivity profiles of communication/similarity matrices with the respective FC matrix.
6.	`AOMIC_PIOP1_compute_coupling_measures_task_x`: This script based on a source script from Esfahlani et al. (2022) and was adjusted accordingly. It calculates coupling measure-specific coupling values by correlating regional connectivity profiles of the communication/similarity matrix (computed with `AOMIC_PIOP1_compute_coupling_measures_resting_state`) with the respective FC matrix

*AOMIC PIOP2*
7.	`AOMIC_PIOP2_compute_coupling_measures_resting_state`: This script is based on a source script from Zamani Esfahlani et al. (2022) and was adjusted accordingly. It performs the computation of communication and similarity matrices based on SC connectivity matrices and calculates coupling measure-specific coupling values by correlating regional connectivity profiles of communication/similarity matrices with the respective FC matrix.
8.	`AOMIC_PIOP2_compute_coupling_measures_task_x`: This script based on a source script from Esfahlani et al. (2022) and was adjusted accordingly. It calculates coupling measure-specific coupling values by correlating regional connectivity profiles of the communication/similarity matrix (computed with `AOMIC_PIOP2_compute_coupling_measures_resting_state`) with the respective FC matrix.

### 5.3. Analysis Scripts

#### 5.3.1. Scripts for HCP - Main Sample 
In order to perform the analyses conducted in the main sample, the scripts should be run in the following order: 

1.	`HCP_region_specific_coupling_all_conditions`: Computation of region-specific SC-FC coupling maps (measure that is able to explain the highest variance in FC across all participants most frequently) and creation of correlation matrix depicting the similarity between vectors of regional SC-FC coupling pattern.
2.	`HCP_whole_brain_coupling_plot_across_conditions`: Computation of coupling measure-specific brain-average coupling values for all eight conditions, taking the mean across the four measure-specific coupling values per condition and creating the violin plot visualizing condition-specific differences in brain-average SC-FC coupling. Run ANOVA to test for significant differences between brain-average condition-specific SC-FC coupling. 
3.	`HCP_whole_brain_coupling_plot_across_coupling_measures`: Computation of coupling measure-specific brain-average coupling values for all eight conditions, taking the mean across the eight condition-specific coupling values per coupling measure and creating the violin plot visualizing coupling measure-specific differences in brain-average SC-FC coupling. Run ANOVA to test for significant differences between brain-average measure-specific SC-FC coupling.
4.	`HCP_whole_brain_coupling_all_conditions_correlation_intelligence`: Computation of coupling measure-specific brain-average coupling values for all eight conditions and performance of partial correlations with individual *g*-scores. 
5.	`HCP_internal_cross_validation_basic_NMA_prediction_model`: Conduction of the internal-cross-validation of the ‘Basic NMA Model’ that is built using two input predictor variables. The predictor variables are derived from individual’s coupling values and extracted by using group-based positive and group-based negative NMA masks. This script contains three parts: **Part 1** partitions the sample into five different folds (considering family relations and intelligence distribution) and creates positive and negative NMAs for each training sample and test sample. **Part 2** uses the NMAs created in part 1 to build multiple linear regression models that are then tested for their ability to predict general intelligence in the test samples. **Part 3** assesses the significance of the prediction with a permutation test.
6.	`HCP_internal_cross_validation_expanded_NMA_prediction_model`: Conduction of the internal-cross-validation of the ‘Expanded NMA Model’ that is built using 14 input predictor variables (two from each of the seven task conditions). The predictor variables are derived from individual’s coupling values and extracted by using group-based positive and group-based negative NMA masks. This script contains three parts: **Part 1** partitions the sample into five different folds (considering family relations and intelligence distribution) and creates positive and negative NMAs for each training fold and test fold. **Part 2** uses the NMAs created in part 1 to build multiple linear regression models that are then tested for their ability to predict general intelligence in the test samples. **Part 3** assesses the significance of the prediction with a permutation test.
7.	`HCP_external_validation_basic_NMA_prediction_model_in_HCP_lockbox`: Conduction of the external validation of the 'Basic NMA Model' that is built using two input predictor variables. The predictor variables are derived from individual's coupling values and extracted by using group-based positive and negative NMA masks. The script contains three parts: **Part 1** partitions the sample into five different folds (considering family relations and intelligence distribution) and creates a positive and negative NMAs for each training sample (4/5 folds) and test sample (lockbox sample in this case). **Part 2** uses the NMAs created in part 1 to build multiple linear regression models that are then tested for their ability to predict general intelligence in the test samples (lockbox sample in this case). **Part 3** assesses the significance of the prediction with a permutation test. 
8.	`HCP_external_validation_basic_NMA_prediction_model_in_AOMIC_PIOP1`: Conduction of the external validation of the 'Basic NMA Model' that is built using two input predictor variables. The predictor variables are derived from individual's coupling values and extracted by using group-based positive and negative NMA masks. The script contains three parts: **Part 1** partitions the sample into five different folds (considering intelligence distribution) and creates a positive and negative NMAs for each training sample (4/5 folds) and test sample (AOMIC PIOP1 sample in this case). **Part 2** uses the NMAs created in part 1 to build multiple linear regression models that are then tested for their ability to predict general intelligence in the test samples (AOMIC PIOP1 sample in this case). **Part 3** assesses the significance of the prediction with a permutation test. 
9.	`HCP_external_validation_basic_NMA_prediction_model_in_AOMIC_PIOP2`: Conduction of the external validation of the 'Basic NMA Model' that is built using two input predictor variables. The predictor variables are derived from individual's coupling values and extracted by using group-based positive and negative NMA masks. The script contains three parts: **Part 1** partitions the sample into five different folds (considering intelligence distribution) and creates a positive and negative NMAs for each training sample (4/5 folds) and test sample (AOMIC PIOP2 sample in this case). **Part 2** uses the NMAs created in part 1 to build multiple linear regression models that are then tested for their ability to predict general intelligence in the test samples (AOMIC PIOP2 sample in this case). **Part 3** assesses the significance of the prediction with a permutation test. 
10.	`HCP_external_validation_expanded_NMA_prediction_model_in_HCP_lockbox`: Conduction of the external validation of the ‘Expanded NMA Model’ that is built using 14 input predictor variables (two from each of the seven task conditions). The predictor variables are derived from individual’s coupling values and extracted by using group-based positive and group-based negative NMA masks. This script contains three parts: **Part 1** partitions the sample into five different folds (considering family relations and intelligence distribution) and creates positive and negative NMAs for each training sample (4/5 folds) and test sample (lockbox sample in this case). **Part 2** uses the NMAs created in part 1 to build multiple linear regression models that are then tested for their ability to predict general intelligence in the test sample (lockbox sample in this case). **Part 3** assesses the significance of the prediction with a permutation test.

*Test Model Difference*
11. `HCP_532_internal_cv_test_significance_model_difference`: This script tests whether there are significant differences in prediction model performance between the internally cross-validated Basic NMA Model (using coupling data from all conditions) and Expanded NMA Model in the main sample. To do so, the actual difference in prediction performance is compared to differences in prediction performances based on permuted scores.
12. `HCP_532_external_validation_test_significant_model_difference`: This script tests whether there are significant differences in prediction model performance between the cross-sample model generalization test of the Basic NMA Model (using coupling data from all conditions) and Expanded NMA Model build in the main sample (HCP532) and tested on data from the lockbox sample (HCP232). To do so, the actual difference in prediction performance is compared to differences in prediction performances based on permuted scores.

#### 5.3.2. Scripts for HCP - Lockbox Sample 
In order to perform the analyses conducted in the lockbox sample, the scripts should be run in the following order:

1.	`HCP_lockbox_region_specific_coupling_all_conditions`: Computation of region-specific SC-FC coupling (measure that is able to explain the highest variance in FC across all participants most frequently) and creation of correlation matrix depicting the similarity between vectors of regional SC-FC coupling pattern. 
2.	`HCP_lockbox_whole_brain_coupling_plot_across_conditions`: Computation of coupling measure-specific brain-average coupling values for all eight conditions, taking the mean across the four measure-specific coupling values per condition and creating the violin plot visualizing condition-specific differences in brain-average SC-FC coupling. Run ANOVA to test for significant differences between brain-average condition-specific SC-FC coupling.
3.	`HCP_lockbox_whole_brain_coupling_plot_across_coupling_measures`: Computation of coupling measure-specific brain-average coupling values for all eight conditions, taking the mean across the eight condition-specific coupling values per coupling measure and creating the violin plot visualizing coupling measure-specific differences in brain-average SC-FC coupling. Run ANOVA to test for significant differences between brain-average measure-specific SC-FC coupling.
4. `HCP_lockbox_whole_brain_coupling_all_conditions_correlation_intelligence`: Computation of coupling measure-specific brain-average coupling values for all eight conditions and performance of partial correlations with individual *g*-scores. 
5.	`HCP_internal_cross_validation_basic_NMA_prediction_model`: Conduction of the internal-cross-validation of the ‘Basic NMA Model’ that is built using two input predictor variables. The predictor variables are derived from individual’s coupling values and extracted by using group-based positive and group-based negative NMA masks. This script contains three parts: **Part 1** partitions the sample into five different folds (considering family relations and intelligence distribution) and creates positive and negative NMAs for each training sample and test sample. **Part 2** uses the NMAs created in part 1 to build multiple linear regression models that are then tested for their ability to predict general intelligence in the test samples. **Part 3** assesses the significance of the prediction with a permutation test.
6.	`HCP_lockbox_internal_cross_validation_expanded_NMA_prediction_model`: Conduction of the internal-cross-validation of the ‘Expanded NMA Model’ that is built using 14 input predictor variables (two from each of the seven task conditions). The predictor variables are derived from individual’s coupling values and extracted by using group-based positive and group-based negative NMA masks. This script contains three parts: **Part 1** partitions the sample into five different folds (considering family relations and intelligence distribution) and creates positive and negative NMAs for each training sample and test sample. **Part 2** uses the NMAs created in part 1 to build multiple linear regression models that are then tested for their ability to predict general intelligence in the test samples. **Part 3** assesses the significance of the prediction with a permutation test.

#### 5.3.3. Scripts for AOMIC - Replication Samples 
In order to perform the analyses conducted in the replication samples, the scripts should be run in the following order: 

*AOMIC PIOP1* 
1.	`AOMIC_PIOP1_region_specific_coupling_all_conditions`: Computation of region-specific SC-FC coupling maps (measure that is able to explain the highest variance in FC across all participants most frequently) and creation of correlation matrix depicting the similarity between vectors of regional SC-FC coupling pattern.
2.	`AOMIC_PIOP1_whole_brain_coupling_plot_across_conditions`: Computation of coupling measure-specific brain-average coupling values for all six conditions, taking the mean across the four measure-specific coupling values per condition and creating the violin plot visualizing condition-specific differences in brain-average SC-FC coupling. Run ANOVA to test for significant differences between brain-average condition-specific SC-FC coupling.
3.	`AOMIC_PIOP1_whole_brain_coupling_plot_across_coupling_measures`: Computation of coupling measure-specific brain-average coupling values for all six conditions, taking the mean across the six condition-specific coupling values per coupling measure and creating the violin plot visualizing coupling measure-specific differences in brain-average SC-FC coupling. Run ANOVA to test for significant differences between brain-average measure-specific SC-FC coupling.
4.	`AOMIC_PIOP1_whole_brain_coupling_all_conditions_correlation_intelligence`: Computation of coupling measure-specific brain-average coupling values for all six conditions and performance of partial correlations with individual RAPM scores.
5.	`AOMIC_PIOP1_internal_cross_validation_basic_NMA_prediction_model`: Conduction of the internal-cross-validation of the ‘Basic NMA Model’ that is built using two input predictor variables. The predictor variables are derived from individual’s coupling values and extracted by using group-based positive and group-based negative NMA masks. This script contains three parts: **Part 1** partitions the sample into five different folds (considering intelligence distribution) and creates positive and negative NMAs for each training sample and test sample. **Part 2** uses the NMAs created in part 1 to build multiple linear regression models that are then tested for their ability to predict general intelligence in the test samples. **Part 3** assesses the significance of the prediction with a permutation test.
6.	`AOMIC_PIOP1_internal_cross_validation_expanded_NMA_prediction_model`: Conduction of the internal-cross-validation of the ‘Expanded NMA Model’ that is built using 10 input predictor variables (two from each of the five task conditions). The predictor variables are derived from individual’s coupling values and extracted by using group-based positive and group-based negative NMA masks. This script contains three parts: **Part 1** partitions the sample into five different folds (considering intelligence distribution) and creates positive and negative NMAs for each training sample and test sample. **Part 2** uses the NMAs created in part 1 to build multiple linear regression models that are then tested for their ability to predict general intelligence in the test samples. **Part 3** assesses the significance of the prediction with a permutation test.

*AOMIC PIOP2*
1.	`AOMIC_PIOP2_region_specific_coupling_all_conditions`: Computation of region-specific SC-FC coupling (measure that is able to explain the highest variance in FC across all participants most frequently) and creation of correlation matrix depicting the similarity between vectors of regional SC-FC coupling pattern.
2.	`AOMIC_PIOP2_whole_brain_coupling_plot_across_conditions`: Computation of coupling measure-specific brain-average coupling values for all four conditions, taking the mean across the four measure-specific coupling values per condition and creating the violin plot visualizing condition-specific differences in brain-average SC-FC coupling. Run ANOVA to test for significant differences between brain-average condition-specific SC-FC coupling.
3.	`AOMIC_PIOP2_whole_brain_coupling_plot_across_coupling_measures`: Computation of coupling measure-specific brain-average coupling values for all four conditions, taking the mean across the four condition-specific coupling values per coupling measure and creating the violin plot visualizing coupling measure-specific differences in brain-average SC-FC coupling. Run ANOVA to test for significant differences between brain-average condition-specific SC-FC coupling.
4.	`AOMIC_PIOP2_whole_brain_coupling_all_conditions_correlation_intelligence`: Computation of coupling measure-specific brain-average coupling values for all four conditions and performance of partial correlations with individual RAPM scores.
5.	`AOMIC_PIOP2_internal_cross_validation_basic_NMA_prediction_model`: Conduction of the internal-cross-validation of the ‘Basic NMA Model’ that is built using two input predictor variables. The predictor variables are derived from individual’s coupling values and extracted by using group-based positive and group-based negative NMA masks. This script contains three parts: **Part 1** partitions the sample into five different folds (considering intelligence distribution) and creates positive and negative NMAs for each training  sample and test sample . **Part 2** uses the NMAs created in part 1 to build multiple linear regression models that are then tested for their ability to predict general intelligence in the test samples. **Part 3** assesses the significance of the prediction with a permutation test.
6.	`AOMIC_PIOP2_internal_cross_validation_expanded_NMA_prediction_model`: Conduction of the internal-cross-validation of the ‘Expanded NMA Model’ that is built using six input predictor variables (two from each of the three task conditions). The predictor variables are derived from individual’s coupling values and extracted by using group-based positive and group-based negative NMA masks. This script contains three parts: **Part 1** partitions the sample into five different folds (considering intelligence distribution) and creates positive and negative NMAs for each training sample and test sample. **Part 2** uses the NMAs created in part 1 to build multiple linear regression models that are then tested for their ability to predict general intelligence in the test samples. **Part 3** assesses the significance of the prediction with a permutation test.

### 5.4. Figures 
1.	`Visualization_distribution_of_general_intelligence_scores`: Creation of histograms depicting the distribution of intelligence scores in the main sample (HCP532), the lockbox sample (HCP232), and the two external replication samples (AOMIC PIOP1 & AOMIC PIOP2). 
2.	`Visualization_prediction_performances`: Creation of bar plots used to visualize the prediction performances of the internal cross-validation in the main sample and the cross-sample model generalization test from the main sample to the lockbox sample (HCP532 &#8594 HCP232).
3.	`HCP_532_NMAs_whole_sample_8_conditions`: This script builds a positive and negative Node-Measure Assignment (NMA) for the complete main sample (HCP532, no cross-validation) and plots them. 
4.	`HCP_232_NMAs_whole_sample_8_conditions`: This script builds a positive and negative Node-Measure Assignment (NMA) for the complete lockbox sample (HCP232, no cross-validation) and plots them.
5.	`AOMIC_PIOP1_NMAs_whole_sample_6_conditions`: This script builds a positive and negative Node-Measure Assignment (NMA) for the first replication sample (AOMIC PIOP1, no cross-validation) and plots them. 
6.	`AOMIC_PIOP2_NMAs_whole_sample_4_conditions`: This script builds a positive and negative Node-Measure Assignment (NMA) for the second replication sample (AOMIC PIOP2, no cross-validation) and plots them. 
7.	`HCP_532_visualization_differences_in_coupling_between_tasks`: This script is used to quantify and visualize task-dependent adaptations in SC-FC coupling in the main sample (HCP532).
8.	`HCP_532_visualization_task_specific_task_general_adaptations_bar_plot`: Creation of bar graphs for visualizing comparison between task-general and task-specific adaptations in SC-FC coupling the main sample (HCP532). 

### 5.5. Posthoc Control Analyses 

*Control Analysis Equal Frame Length* 
Scripts found in this folder were used to conduct a control analysis where all scans were shortened to equal frame length (176 frames) in the main sample (HCP532). Other than the differing data, scripts were not changed and named similarly (see above for description). 

### 5.6. Functions 
External functions and their licences can be found in this folder.

## 6. Software Requirements 

+ Matlab version 2021a
+ R version 4.0.2 (For the computation of the latent *g*-factor)

## 7.	Functions 

Functions used in the scripts can be found in the functions folder. Some of the functions can be found elsewhere e.g., as part of the brain connectivity toolbox (Rubinov & Sporns, 2010, https://sites.google.com/site/bctnet/). Comments on the authorship and licenses of these functions are provided within the folder.

**References**
Civier, O., Smith, R. E., Yeh, C.-H., Connelly, A., & Calamante, F. (2019). Is removal of weak connections necessary for graph-theoretical analysis of dense weighted structural connectomes from diffusion MRI? NeuroImage, 194, 68–81. https://doi.org/10.1016/j.neuroimage.2019.02.039

Cole, M. W., Ito, T., Schultz, D., Mill, R., Chen, R., & Cocuzza, C. (2019). Task activations produce spurious but systematic inflation of task functional connectivity estimates. NeuroImage, 189, 1–18. https://doi.org/10.1016/j.neuroimage.2018.12.054

Dubois, J., Galdi, P., Han, Y., Paul, L. K., & Adolphs, R. (2018). Resting-State Functional Brain Connectivity Best Predicts the Personality Dimension of Openness to Experience. Personality Neuroscience, 1, e6. https://doi.org/10.1017/pen.2018.8

Esteban, O., Markiewicz, C. J., Blair, R. W., Moodie, C. A., Isik, A. I., Erramuzpe, A., Kent, J. D., Goncalves, M., DuPre, E., Snyder, M., Oya, H., Ghosh, S. S., Wright, J., Durnez, J., Poldrack, R. A., & Gorgolewski, K. J. (2019). fMRIPrep: A robust preprocessing pipeline for functional MRI. Nature Methods, 16(1), 111–116. https://doi.org/10.1038/s41592-018-0235-4

Glasser, M. F., Coalson, T. S., Robinson, E. C., Hacker, C. D., Harwell, J., Yacoub, E., Ugurbil, K., Andersson, J., Beckmann, C. F., Jenkinson, M., Smith, S. M., & Van Essen, D. C. (2016). A multi-modal parcellation of human cerebral cortex. Nature, 536(7615), 171–178. https://doi.org/10.1038/nature18933

Glasser, M. F., Sotiropoulos, S. N., Wilson, J. A., Coalson, T. S., Fischl, B., Andersson, J. L., Xu, J., Jbabdi, S., Webster, M., Polimeni, J. R., Van Essen, D. C., & Jenkinson, M. (2013). The minimal preprocessing pipelines for the Human Connectome Project. NeuroImage, 80, 105–124. https://doi.org/10.1016/j.neuroimage.2013.04.127

Parkes, L., Fulcher, B., Yücel, M., & Fornito, A. (2018). An evaluation of the efficacy, reliability, and sensitivity of motion correction strategies for resting-state functional MRI. NeuroImage, 171, 415–436. https://doi.org/10.1016/j.neuroimage.2017.12.073

Smith, R. E., Tournier, J.-D., Calamante, F., & Connelly, A. (2012). Anatomically-constrained tractography: Improved diffusion MRI streamlines tractography through effective use of anatomical information. NeuroImage, 62(3), 1924–1938. https://doi.org/10.1016/j.neuroimage.2012.06.005

Snoek, L., van der Miesen, M. M., Beemsterboer, T., van der Leij, A., Eigenhuis, A., & Steven Scholte, H. (2021). The Amsterdam Open MRI Collection, a set of multimodal MRI datasets for individual difference analyses. Scientific Data, 8, 85. https://doi.org/10.1038/s41597-021-00870-6

Thiele, J. A., Faskowitz, J., Sporns, O., & Hilger, K. (2022). Multitask Brain Network Reconfiguration Is Inversely Associated with Human Intelligence. Cerebral Cortex, bhab473. https://doi.org/10.1093/cercor/bhab473

Tournier, J.-D., Smith, R., Raffelt, D., Tabbara, R., Dhollander, T., Pietsch, M., Christiaens, D., Jeurissen, B., Yeh, C.-H., & Connelly, A. (2019). MRtrix3: A fast, flexible and open software framework for medical image processing and visualisation. NeuroImage, 202, 116137. https://doi.org/10.1016/j.neuroimage.2019.116137

Van Essen, D. C., Smith, S. M., Barch, D. M., Behrens, T. E. J., Yacoub, E., & Ugurbil, K. (2013). The WU-Minn Human Connectome Project: An Overview. NeuroImage, 80, 62–79. https://doi.org/10.1016/j.neuroimage.2013.05.041

Zamani Esfahlani, F., Faskowitz, J., Slack, J., Mišić, B., & Betzel, R. F. (2022). Local structure-function relationships in human brain networks across the lifespan. Nature Communications, 13(1), 2053. https://doi.org/10.1038/s41467-022-29770-y



## Copyright 
Copyright (cc) by Johanna L. Popp 

<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br />

Files of SC_FC_Coupling_Task_Intelligence by Johanna L. Popp are licensed under <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

Note that external functions have other licenses that are provided in the `Functions` folder. 





# cs579-project wrist identification
This is my project for the course CS 579 Biometrics. This is reimplementation of a matlab repository [Wrist-Identification-for-Forensic-Investigation](https://github.com/matkowski-voy/Wrist-Identification-for-Forensic-Investigation). The repository is implementation of paper [Wojciech Michal Matkowski, Frodo Kin Sun Chan and Adams Wai Kin Kong. A Study on Wrist Identification for Forensic Investigation. Image and Vision Computing, vol. 88, August 2019, pp 96-112.](https://doi.org/10.1016/j.imavis.2019.05.005)

Thanks to @matkowski-voy for letting me use the dataset.

## Segmentation
Segmentation aims to extract wrist region from the background. It uses **"SEToriginalWristImages"** and generates **"SETsegmentedWristImages"**. It uses SLIC algorithm to generate superpixels (segments). **It assumes superepixels respect to the boundaries of wrist.** It extracts features from superpixels and makes binary classification. Features are extracted manually. Features for a superpixel are mean, standard deviation of color and gradient values of 8 (2 up, 2 down, 2 right, 2 left) neighbors of a superpixel. If a superpixel does not have a neigbor, **I assume its neighbor is itself**. If there are more than 2 neighbors in 1 direction, I select the largest top 2.

### notes
- If your aim is only making matching, there is already segmented images. You can use them directly. 
- I make supervised classification. So at the beginning I need labeled superpixels. Since segmented images are provided, I can train supervised models.
- a superpixel should be either inside skin or non-skin region. If it's region is intersecting with both, it is problematic. So increasing number of superpixels might be better. SLIC in python might be worse then the original matlab implementation.
- increasing minimum neighboring pixel number to determine adjacency of 2 superpixels makes slight improvement. (If 2 superpixels have only 1 neighboring pixel, should I assume that they are neighbors?)

## Region of Interest (ROI) extraction
It uses **"SETsegmentedWristImages"** and generates **SETsegmentedAlignedWristImages**. I started implemeting this part but I couldn't finish it. Here they use 2 different algorithms. So for each wrist image, they generate 2 *segmented & aligned* images. That's why there are folders named as both **SET1p** and **SET1**. They contain the 2 different representations of the same samples.

## Feature extraction
It uses **SETsegmentedAlignedWristImages** and extracts LBP, SIFT, Gabor features. I couldn't extract, SIFT features. I extracted Gabor and LBP features. There might be something wrong with Gabor features because they are very ineffective. (see figure 4 and 5 in my report). LBP features are extracted from 7 different grid structures. On 2 grid structures with largest cells, I used uniform and rotation-invariant patterns. On the remaining 5 grid cells I used uniform patterns to extract LBP fetures. Extracting fetures takes lots of time so I save them to a file and use them later. (30 mins for Gabor 10 mins for LBP)

## matching
I built a classifier for each class. Classifiers are simple linear classifiers. I also saved classifiers as file because it takes long. (20 mins) By using a set of classifiers, I make matching. The original paper calls set of classifiers used in matching as *Recognition System*. There are 2 representations for samples. I built 2 different classifiers (PLS, SVM). So at the end 4 different recognition systems are built. I used EVT theorem to use all of them on decistion making. I used `sklearn.svm.LinearSVC`, instead using libSVM might have been better.

## resources
- You can read [my report](analysis/cs_579_project_final.pdf) for more detailed explanations. There is also [a presentation](https://docs.google.com/presentation/d/1dxfrGeUa8RAUU8n0mHnkPm0kMiJ8_nK2-wuiMAITIrs/edit?usp=sharing)
- You can download my [generated features and classifiers](https://drive.google.com/open?id=11X5NLfGpmvyHBH5IioDqIsos0GcHbbcF)

## notes
Only **segmentation** and **matching** parts are fully implemented. 
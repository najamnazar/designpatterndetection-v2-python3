# designpatterndetection-v2-python3
This is the python 3 version of my old work done earlier in python 2.7. The old work is accessible at https://github.com/najamnazar/designpatterndetection

# Design-Pattern-detection

This application identifies design pattern instances from a given dataset. **Updated for Python 3 compatibility.**

## About This Project

This codebase implements the **Feature-Based Design Pattern Detection (DPD_F)** approach described in:

> **Najam Nazar, Aldeida Aleti, Yaokun Zheng**  
> *"Feature-based software design pattern detection"*  
> **Journal of Systems and Software**, Volume 185, March 2022, 111179  
> DOI: [10.1016/j.jss.2021.111179](https://doi.org/10.1016/j.jss.2021.111179)

**Key Highlights:**
- Uses 15 source code features (structural and lexical) to detect GoF design patterns
- Applies Word2Vec on Software Syntactic and Lexical Representation (SSLR)
- Achieves over 80% Precision and 79% Recall on 12 design patterns
- Outperforms existing approaches (FeatureMaps, MARPLE-DPD) by 35% and 15% respectively
- Includes DPD_F-Corpus: 1,300 Java files labeled by expert software engineers

**Citation:**
```bibtex
@article{nazar2022feature,
  title={Feature-based software design pattern detection},
  author={Nazar, Najam and Aleti, Aldeida and Zheng, Yaokun},
  journal={Journal of Systems and Software},
  volume={185},
  pages={111179},
  year={2022},
  publisher={Elsevier},
  doi={10.1016/j.jss.2021.111179}
}
```

#### Note: The files below require the matplotlib package for plotting and readable output

## Data Source
- [Java file Corpus](http://groups.inf.ed.ac.uk/cup/javaGithub/) - Large dataset of Java projects
- **Subset Used**: 1300 selected Java files listed in `input-1300.csv` with labeled design patterns for training/testing
- **Focus**: Files specifically chosen because they contain design pattern implementations (e.g., `jbehave-core/GroovyStepsFactory.java` implementing AbstractFactory pattern)

## Dataset Pipeline
1. **Java source files** from the corpus (containing design pattern examples)
2. **`detector.py`** → extracts features → creates `.verbose` files  
3. **`make_class_features.py`** → combines `.verbose` files + `input-1300.csv` labels → creates `P-MARt-dataset.csv`/`dataset.csv`
4. **`classifier.py`** → trains/tests machine learning models on the labeled dataset

## Prerequisites

Install dependencies:
```bash
pip install -r requirements.txt
```

**Note**: The project requires `plyj` from GitHub (not PyPI) for proper Java parsing:
```bash
pip install git+https://github.com/musiKk/plyj.git
```


## Dataset Generation
The dataset used for the classification is generated in a series of steps. First, make_ngram.py is used to construct a Word2Vec model using a set of files (verbose files) containing class names and ngrams associated with the class. make_glove.py performs a similar task, but instead constructs a GloVe model given the verbose files. The word embedding model is used to construct a dataset by running make_class_features.py followed by 

### detector.py

Outputs `.verbose` files in the output directory taking `.java` files from input directory

Usage:
```python
python detector.py --input ./input --output ./output --tasks all
``` 

### make_class_features.py

Outputs vector representation Java classes from the ngram model in `dataset.csv` along with labels merged from `input-1300.csv`

Usage:  
```python
python make_class_features.py [VERBOSE_ROOT]
```

Arguments:  
- VERBOSE_ROOT: root of the directory containing the verbose files. 

NOTE: This script uses the Word2Vec implementation in the [Gensim package](https://github.com/RaRe-Technologies/gensim "Gensim Github Repo"). 
And may need to install sudo apt-get install -y liblzma-dev to run it for linux machines.


## Classification
Classification is performed in the python files listed below. The file calls the dataset.csv file, which is used to train a machine learning model and generate predictions. 

Each script outputs 
- confusion matrix csv and plot
- Per class Precision, Recall barplots
- report containing the precision, recall, and f-score values for the prediction.


### classifier.py
Uses a various to classify the design patterns contained in the test files.  

Usage:
```python
python classifier.py [CLASSIFIERNAME]
```  

NOTE: Assumes the dataset is called 'dataset.csv'  

Arguments: 

- CLASSIFIERNAME - Which Classification algorithm you want to use.

CLASSIFIERNAME supported are
- RF: RandomForest
- SVM: Support Vector Machine
- ADABOOST: Adaboost
- ADABOOST_LOGISTIC: Adaboost
- LOGISTIC: Logistic Regression
- GBTREE: GradientBoosted Tree
- RIDGE: Ridge classifier
- VOTER: Voting classifier composed of RF, SVM, ADABOOST


## Miscallenous Scripts [currently not used]

### print_ngram_features.py

Prints the vector representations of the ngrams in readable format.

Usage:  
print_ngram_features.py [MODEL_JAVA_FILE] [MODEL_GLOVE_FILE] [OUTPUT_JAVA_FILE] [OUTPUT_GLOVE_FILE]

Arguments:  
<li>MODEL_JAVA_FILE: file name of the ngram vector model  
<li>MODEL_GLOVE_FILE: file name of the GloVe vector model  
<li>OUTPUT_JAVA_FILE: text file to write the vector representations of the ngrams  
<li>OUTPUT_GLOVE_FILE: text file to write the vector representations of GloVe




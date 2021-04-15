# Supervised Latent Dirichlet Allocation for Classification

**Official repo:** https://github.com/blei-lab/class-slda

This is a C++ implementation of supervised latent Dirichlet allocation (sLDA)
for classification.

Note that this code depends on the [GNU Scientific Library](http://www.gnu.org/software/gsl/).

# Compiling

```bash
git clone https://github.com/chbrown/slda
cd slda
make
```

You may need to install the `gsl` first. E.g., on a Mac:

```bash
brew install gsl
```


# Estimation

Estimate the model by executing:

```bash
slda est <data path> <label path> <settings path> <alpha> <k> <initialization> <output directory>
```

* `<data path>` should point to a single file containing your training data.
    - This should be a file where each line is of the form:

            <M> <term_1>:<count> <term_2>:<count> ... <term_N>:<count>

    - where `<M>` is the number of unique terms in the document, and the
      [count] associated with each term is how many times that term appeared
      in the document. (For an example, see [test/images/train-data.dat](test/images/train-data.dat).)
* `<label path>` points to a file of labels
    - Each line should consist of a single integer, starting with 0, up to C-1, if we have C classes.
    - This file should have the same number of lines as the file specified by `<data path>`.
* `<settings path>` should point to a file with various settings, e.g., [settings.txt](settings.txt)
* `<alpha>` is a floating point hyperparameter (a prior)
* `<k>` is the number of topics
* `<initialization>` specifies the initialization method. There are three options:
    - "seeded"
    - "random"
    - `<model path>` (a path to some pre-existing model)
* `<output directory>` should point to a directory where the estimator's output will be stored.
  This directory will be created if it does not already exist.
    - The estimator outputs models in two types of files:
        + `<iteration>.model` is the model saved in the binary format, which is easy and
          fast to use for inference.
        + `<iteration>.model.text` is the model saved in the text format, which is
          convenient for printing topics or further analysis using a scripting language.
    - It also produces variational posterior Dirichlets in a file called:
        + `<iteration>.gamma`
    - Running the estimator on the [8-class image dataset](http://labelme.csail.mit.edu/) produces the output:

            010.gamma
            010.model
            010.model.text
            020.gamma
            020.model
            020.model.text
            final.gamma
            final.model
            final.model.text
            likelihood.dat
            word-assignments.dat

Example usage:

```bash
./slda est test/images/train-data.dat test/images/train-label.dat \
    settings.txt 1.0 10 random tmp/
```


# Inference

To perform inference on a different set of data (in the same format as
for estimation), execute:

```bash
slda inf <data path> <label path> <settings path> <model path> <output directory>
```

* `<data path>`, `<label path>`, and `<settings path>` are all the same as in the estimation step.
* `<model path>` is the binary `final.model` file from the estimation step.
* `<output directory>` is the output directory, where the predicted labels will be stored.
    - Each output file has one line per input document.
        + `inf-gamma.dat` describes the variational posterior Dirichlets
        + `inf-labels.dat` displays the predicted labels
        + `inf-likelihood.dat` depicts each document's likelihood

Example usage:

```bash
./slda inf test/images/test-data.dat test/images/test-label.dat \
    settings.txt tmp/final.model tmp/
```

This will also produce a final line of output, evaluating against the labels
specified in the `<label path>` argument:

    average accuracy: 0.679


## Sample data

The sample data in [test/images](test/images) was downloaded from
`http://www.cs.cmu.edu/~chongw/data/images.tgz` on July 12, 2013.

### Description of data from [original site](http://www.cs.cmu.edu/~chongw/slda/):

> A preprocessed 8-class image dataset from [Labelme](http://labelme.csail.mit.edu/).

> UIUC Sports annotation files: [annotations](http://www.cs.cmu.edu/~chongw/data/uiuc-sports-annotations.txt) and [meta information](http://www.cs.cmu.edu/~chongw/data/uiuc-sports-info.txt). The source image files can be found [here](http://vision.stanford.edu/lijiali/event_dataset/).
> (Note: there might be some discrepancies and I don't seem to know why...)


## License

Copyright © 2009, Chong Wang, David Blei and Li Fei-Fei

Licensed under both the [GPL v2](LICENSE) and [GPL v3](LICENSE),
as well as any future version of the GNU General Public License.

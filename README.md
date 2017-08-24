# Optimize tensorflow for your MacBook by using SSE3, SSE4.1 SSE4.2 AVX, AVX2 and FMA instructions.

" The TensorFlow library wasn't compiled to use X instructions, but these are available on your machine and could speed up CPU computations."

I've been using tensorflow for a while now and got some messages about speeding up the CPU-computations using SSE3, SSE4.1 SSE4.2 AVX, AVX2 and FMA instructions. In short these are extensions to the x86 instruction set architecture for microprocessors from Intel and AMD and contain optimized instructions for processing matrix or vector operations (which of course is good for tensorflow). The pre-built versions of tensorflow have a wide compatibility which means disabling most of these optional speedups. Hence we will build tensorflow from source in a Conda environment. 

Reference to tensorflows guide: https://www.tensorflow.org/install/install_sources

I used my MacBook Pro late 2013 with an 2 GHz Intel Core i7.

To check if your CPU supports the instructions run `sysctl machdep.cpu.features` and look for the SSE/FMA instructions.

`sysctl -a | grep machdep.cpu.leaf7_features` and look for the AVX instructions.

### What you'll need installed

#### Xcode
Should be installed, if not it's available from the App Store.
#### Python
I was using the Anaconda distribution of Python 3.6.2
#### Homebrew
`ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
#### Java
Available at [oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
#### Bazel
Used to compile and package tensorflow. Install by running `brew install bazel`
#### Python dependencies
* six
* numpy
* wheel, to manage python compressed packages in .whl format.

`conda install six numpy wheel`

### Install Tensorflow

Create a Conda environment and activate it.

`conda create -n tensorflow`

`source activate tensorflow`

Checkout tensorflow from github.

`git clone https://github.com/tensorflow/tensorflow`

Enter local copy

`cd tensorflow`

Configure build

`./configure`

for user default just press enter for all.

Build the pip package

`bazel build -c opt --copt=-mavx --copt=-mavx2 --copt=-mfma --copt=-msse4.2 -k \
  //tensorflow/tools/pip_package:build_pip_package`

this will take about 40 minutes.

The `build` command built a script named build_pip_package. Next we build a .whl file within the /tmp/tensorflow_pkg folder.

`bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg`

Install the pip package, the filename of the .whl file depends on the platform but there should only be one file in the directory so just use tab to autocomplete.

`sudo pip install /tmp/tensorflow_pkg/<Your file name>.whl`

Next we have to test that tensorflow have been installed correctly. **You have to cd out of the tensorflow directory** otherwise you'll get a weird error.

`python`

```
import tensorflow as tf
hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```

If the systems outputs `Hello, TensorFlow!` everything should be correct.

### Using tensorflow in a jupyter notebook.
Since tensorflow only exists in the environment you have to been in it to run tensorflow. Hence to use a Jupyter Notebook it also has to be installed inside the environment.

To activate and deactivate the environment use:

`source activate tensorflow` and `source deactivate tensorflow`
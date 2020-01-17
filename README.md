# Characterizing A Perceptron

The code trains and tests a simple classifier based on a single-level neural
net (or *perceptron*).  Perceptrons were one of the earliest forms of neural
network and are an important building block in model deep-learning networks.

This lab is quite long, but it doesn't require any coding.  There are quite a
lot of questions to answer, though, and the you'll need to run quite a few
experiments, which will take time (especially if you wait till the last minute
and the autograder is bogged down), so start early!

This lab will introduce you to the tools you will use throughout the rest of
class to understand the behavior of more and more complex machine learning
models.  It serves two purposes:

1. It is an advanced tutorial for running experiments on the
autograder infrastructure and interpreting the results.

2. It provides hands-on experience with the impact the compile and
system settings have an performance and efficiency.

Both are important for the course, but #1 is critical for your ability to do
the remaining labs.

This lab will be completed on your own.

Check gradescope for due date(s).

## Grading

Your grade for this lab will be based on your completion of the data
collection steps described in this document and the completed worksheet.

| Part           | value |
|----------------|-------|
| Data collection| 25%   |
| Worksheet      | 75%   |

Your final score on the lab will the weighted average of your % score
on the gradescope assignment for each part.

## Skills to Learn and Practice

1. Use the autograder to measure the performance and performance counters for a test program.

2. Use the `gprof` profiler to understand where a program spends it's time.

3. Analyze how the compiler converts your code to assembly.

4. Apply compiler optimizations to improve performance

5. Compare the performance of different versions of the test program.

6. Estimate the performance of the test program based on input characteristics.

7. Vary the clock speed at which the autograder runs.

8. Optimize the energy consumed by the test program.

9. Prepare legible graphs from gathered data and draw conclusions from them.

## Software You Will Need

1. A computer with Docker installed (either the cloud docker container
via ssh, or your own laptop).  See the intro lab for details.

2. The lab for the github classroom assignment for this lab.  Find the
link on the course home page: https://github.com/CSE141pp/Home/.

3. A PDF annotator/editor to fill out `worksheet.pdf`.  You'll
submit this via a *a separate assignment* in Gradescope.  We *will
not* look at the version in your repo.

## Tasks to Perform

### Fill Out the Worksheet

Be sure to fill out the entire worksheet.  The easiest way to fill it
out is with some sort of PDF annotation tool.  You have some
flexibility in how you fill it out as long as

1. The location of the answers remains the same

2. Your graphs and tables fit in the space provided.

You can, for instance, recreate the tables (and create the graphs) is
Excel and paste them in.

Hand-drawn graphs are not acceptable.

Hand-written/inked numbers are acceptable __if they are legible__.  We
won't spend much time diciphering your hand writing.

Be sure to follow the guidelines we discussed in class for creating
nice-looking, legible graphs.

**DO NOT** attach extra sheets.  Your work must fit in space provided.

Also, remember that you can only submit to the worksheet assignment
**once**, while you can resubmit the coding portion as often as you
would like.

### Run the Starter Code Locally and Verify the Output

Accept the starter code via the link above.

This will set you up with a copy of the starter repository.

Clone your repo locally.

```
git clone [link to your repo]
```

You'll find several files.  You will be editing these two:

1.  `code.cpp` -- The functions that actually do the work.
2.  `config` -- Configuration file the experiment you'll run.

### Test the Starter Code Locally

As you did in Lab 0, navigate to the clone of your repo while inside
the course development environment docker image.

Do `runlab` to build and run the code.

### Exploring The Lab

Take a look at `Makefile` in the root of the repo.  This is the make file used
when your repo is run on the reference processor.

This lab will run two experiments: One to measure instruction mix
(`INST_MIX_CMD_LINE_ARGS`) and another to measure performance
(`PE_CMD_LINE_ARGS`).

```
INST_MIX_CMD_LINE_ARGS=--stat-set inst_mix.cfg $(CMD_LINE_ARGS)
```

Let's break that down:

* `--stat-set inst_mix.cfg`: This tells the autograder to measure
   instruction mix information.

*  `$(CMD_LINE_ARGS)`: This will expand to what you set in `config.env`.

The instruction mix stats it collects will appear in `inst_mix.csv`
after your code runs (you'll be able to find it via the zip file URL
the autograde will give you).

Performance data is collected with `--stat-set PE.cfg`.  It ends up in
`pe.csv`.

These two command line options won't work on your local machine, since
they access the hardware performance counters on our server.  However,
you can the code locally with

```
runlab --devel
```

Commit the resulting `inst_mix.csv` and `pe.csv` as `outputs/devel-inst_mix.csv` and `outputs/devel-pe.csv`. You will have to use -f to force adding a .csv file. Please make sure you only add these files.

### Test the Starter Code on the Autograder

Submit it to the autograder to confirm that it works.

### Configuration Options

Take a look at the file `config.env` in the root of the repo.  This file lets you change how the code will be built and run on the reference processor:

1. `COMPILER=gcc-9`: Use the latest version of the `gcc` compiler (version 9)
2. `OPTIMIZE=-O0` : Compile without optimizations
3. `CMD_LINE_OPTIONS=--dataset mnist` : Run the test code with the mnist dataset.
4. `#GPROF=yes` : Turn on the profiler (commented out with the `#`)

### Datasets

We will use several datasets in the course:

Name        | input dim    | categories |Description                         | URL                                                           |
------------|--------------|------------|------------------------------------|---------------------------------------------------------------|
`mnist`     | 28x28 (gray) | 10         | Handwritten digits                 | http://yann.lecun.com/exdb/mnist/                             |
`emnist`    | 28x28 (gray) | 62         | Handwritten digits and letters     | https://www.nist.gov/itl/products-and-services/emnist-dataset |
`cifar10`   | 32x32 (rgb)  | 10         | Image categorization               | https://www.cs.toronto.edu/~kriz/cifar.html                   |
`cifar100`  | 32x32 (rgb)  | 100        | Image categorization               | https://www.cs.toronto.edu/~kriz/cifar.html                   |
`imagenet`  | 256x256 (rgb)| 1000       | Image categorization (tiny subset) | http://www.image-net.org/                                     |

You can run multiple datasets consecutively by passing the `--dataset` option
multiple times.  For example, `--dataset mnist --dataset emnist` will run both
`mnist` and `emnist` datasets.

### Measuring Performance

Your first task is to measure the baseline performance of the perceptron on
each of the datasets.  Modify the `cmd_line` option in `config` to run all the
data sets.

Commit the changes and submit the changes.

Once the autograder completes, download the zip file via the link, and open it.

If you open up `inst_mix.csv` you'll see that it's pretty hard to read:

```
GPROF,OPTIMIZE,DEVEL_MODE,STATS,dataset,training_inputs_count,nJ,runtime,insts,mem_ops,branches,uncond_branches,
no,-O0,,INST_MIX,mnist,200,1728088378,0.13515782356262207,881283776,648288017,83814492,69271166,
no,-O0,,INST_MIX,emnist,150,4785522460,0.34865498542785645,2378598263,1749969246,226119981,186974726,
```

It's in comma-separated-value format which is good for importing into
excel or Google Sheets.  (**TIP**: The easiest way to get it into a
spread sheet is to copy and paste it into the top left of a Google
Sheet.  Then select`"Data->Split Text into columns`.  Voila!).

From inside your docker container, you can  make it easier to read with:

```
pretty-csv inst_mix.csv
```

You should see something like this (but longer):

```
GPROF|OPTIMIZE|DEVEL_MODE|STATS   |dataset |training_inputs_count|nJ      |runtime|insts   |mem_ops |branches|uncond_branches|
-----|--------|----------|--------|--------|---------------------|--------|-------|--------|--------|--------|---------------|
no   |-O0     |          |INST_MIX|mnist   |2e+02                |1.73e+09|0.135  |8.81e+08|6.48e+08|8.38e+07|6.93e+07       |
no   |-O0     |          |INST_MIX|emnist  |1.5e+02              |4.79e+09|0.349  |2.38e+09|1.75e+09|2.26e+08|1.87e+08       |
```

From now on, we format csv files like this in the lab write ups:

GPROF|OPTIMIZE|DEVEL_MODE|STATS   |dataset |training_inputs_count|nJ      |runtime|insts   |mem_ops |branches|uncond_branches|
-----|--------|----------|--------|--------|---------------------|--------|-------|--------|--------|--------|---------------|
no   |-O0     |          |INST_MIX|mnist   |2e+02                |1.73e+09|0.135  |8.81e+08|6.48e+08|8.38e+07|6.93e+07       |
no   |-O0     |          |INST_MIX|emnist  |1.5e+02              |4.79e+09|0.349  |2.38e+09|1.75e+09|2.26e+08|1.87e+08       |


Copy and commit `pe.csv` and `inst_mix.csv` to `outputs/baseline-pe.csv` and `outputs/baseline-inst_mix.csv`. You will have to use -f to force adding a .csv file. Please make sure you only add these files.

### Enabling the Profiler

To understand why the code runs like it does, you will use the `gprof` profiler to
collect information about where all the time goes.

To do that, uncomment the `GPROF` line in `config.env` to turn on the
profiler.

Commit the change, and submit it to the autograder.  When it comes
back `pe.gprof` should look like this.  Your output will look
different, since this is the ouptut for just one of the input datasets
(`cifar-10`):

```
Flat profile:

Each sample counts as 0.01 seconds.
  %   cumulative   self              self     total
 time   seconds   seconds    calls   s/call   s/call  name
 16.41      0.42     0.42     2000     0.00     0.00  fc_layer_t::activate(tensor_t<float> const&)
 15.43      0.81     0.40 184370720     0.00     0.00  tensor_t<float>::get(int, int, int)
 11.33      1.10     0.29 122880000     0.00     0.00  fc_layer_t::map(point_t)
 10.16      1.36     0.26     1000     0.00     0.00  fc_layer_t::calc_grads(tensor_t<float> const&)
  8.98      1.59     0.23     1000     0.00     0.00  fc_layer_t::fix_weights()
  7.62      1.79     0.20 122884070     0.00     0.00  point_t::point_t(int, int, int)
  7.42      1.98     0.19 184370720     0.00     0.00  tensor_t<float>::operator()(int, int, int)
  6.05      2.13     0.15 61474068     0.00     0.00  tensor_t<float>::get(int, int, int) const
  6.05      2.29     0.15 30720000     0.00     0.00  update_weight(float, gradient_t&, float)
  4.30      2.40     0.11 61450000     0.00     0.00  tensor_t<float>::operator()(int, int, int) const
  1.95      2.45     0.05    13092     0.00     0.00  tensor_t<float>::tensor_t(tensor_t<float> const&)
  1.17      2.48     0.03                             memcpy
  0.98      2.50     0.03    20000     0.00     0.00  std::vector<gradient_t, std::allocator<gradient_t> >::operator[](unsigned long)
  0.78      2.52     0.02                             tensor_t<gradient_t>::operator()(int, int, int) const
  0.59      2.54     0.01                             operator<<(std::ostream&, gradient_t const&)
  0.39      2.55     0.01    20000     0.00     0.00  fc_layer_t::activator_function(float)
  0.20      2.56     0.01                             rand_f(int)
  0.20      2.56     0.01                             void std::_Destroy<gradient_t*, gradient_t>(gradient_t*, gradient_t*, std::allocator<gradient_t>&)
...
```

Review the slides/video from class for details of how to interpret the
profiler's output.

There are few things to look for in your output:

1. The `fc_*` functions are methods of the full-connected layer (i.e.,
a perceptron)

2. The `tensor_t<float>` funtions are methods of the tensor class
Canela uses as its main data type.  It's essentially a 3-dimensional
array of `float`.

3. Three of the top functions (`fc_layer_t::activate`,
`fc_layer_t::calc_grads`, `fc_layer_t::fix_weights`) are executed a
relatively small number of times but account for a lot of time.  In
the example above, these acco unt for 35% of execution time.  How much
are they in your data?

4. The remaining functions in the top 10 are called many, many times,
but are very brief.  These account for 58% of execution time, in the
example.  How much are they in your data?

The source code for Canela is in `/course/CSE141pp-SimpleCNN/CNN`.  Find the
implementations of the top five functions.  You'll notice that the
frequently-executed functions, short functions are very simple (i.e.,
no loops, few or no branches), while the infrequently-executed
functions are more complex.

Unfortunately, there are not obvious ways to improve the performance
of the frequent, short functions, because they are very simple -- they
just don't do that much.

Rename `pe.prof` to `outputs/baseline.gprof` and commit it.  Copy and commit
`pe.csv` and `inst_mix.csv` to `outputs/baseline-gprof-pe.csv` and
`outputs/baseline-gprof-inst_mix.csv`. You will have to use -f to force adding a .csv file. Please make sure you only add these files.

### Taking a Closer Look at the Code

Let's look more closely at what's going on with the frequently-called
functions.  Open up your `outputs/baseline.gprof` and page down.
There will be pages and pages of really long, messy-looking function
names that are all for functions that contribute almost nothing to
execution time.  Search for "Call graph".  You should see something
like this:

```
index % time    self  children    called     name
                0.00    0.61       1/1           generic_start_main [2]
[1]    100.0    0.00    0.61       1         main [1]
                0.00    0.46       1/1           train_model(model_t*, dataset_t const&, int) [5]
                0.00    0.14       1/1           test_model(model_t*, dataset_t const&) [10]
                0.00    0.00       2/2           dataset_t::read(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, unsig
                0.00    0.00       1/1           build_model(dataset_t const&) [38]
                0.00    0.00       4/4           dataset_t::~dataset_t() [1419]
                0.00    0.00       2/20          std::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<s
                0.00    0.00       2/71          bool __gnu_cxx::operator!=<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*,
                0.00    0.00       2/4           dataset_t::dataset_t() [1418]
                0.00    0.00       2/9           bool std::operator==<char, std::char_traits<char>, std::allocator<char> >(std::__cxx11::basic_string<char, st
                0.00    0.00       2/3           std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > std::operator+<char, std::cha
                0.00    0.00       2/2           dataset_t::operator=(dataset_t&&) [1506]
                0.00    0.00       2/4092        std::vector<test_case_t, std::allocator<test_case_t> >::size() const [1028]
                0.00    0.00       2/2           void DataCollector::register_tag<unsigned long>(std::__cxx11::basic_string<char, std::char_traits<char>, std:
                0.00    0.00       2/2           dataset_t::get_total_memory_size() const [1513]
                0.00    0.00       2/2           dataset_t::size() const [1514]
                0.00    0.00       2/2           ArchLabTimer::ArchLabTimer() [1490]
                0.00    0.00       2/60          std::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<s
                0.00    0.00       1/16          std::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<s
                0.00    0.00       1/1           void archlab_add_option<std::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<c
                0.00    0.00       1/59          std::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<s
                0.00    0.00       1/50          std::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<s
                0.00    0.00       1/44          __gnu_cxx::__normal_iterator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*
                0.00    0.00       1/1           void DataCollector::register_tag<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char
                0.00    0.00       1/1           model_t::geometry[abi:cxx11]() const [1626]
                0.00    0.00       1/1           ArchLabTimer& ArchLabTimer::attr<char [6]>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allo
                0.00    0.00       1/1           ArchLabTimer& ArchLabTimer::attr<char [5]>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allo
                0.00    0.00       1/43          __gnu_cxx::__normal_iterator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*
```

This the call graph entry for `main()`.  It appears on the second
line, which shows that it's ID number is 1 (it's the first function
listed in the profile) and it took 100% of execution time.  The items
above it are places it is called from (just one, in this case), and
the lines after are functions it calls.  The "called" column shows
many of the calls `main()` accounts for.  For instance,
`std::vector<test_case_t, std::allocator<test_case_t> >::size() const`
was called 4092 times and two of them are from `main()`.

Page down to search for the function that accounts for the most
execution time in your flat profile (if your top function is one of
the `get` functions, use the next function instead).  In the
example, it's `fc_layer_t::activate`.

You'll find something like this:

```
                0.07    0.08    1000/2000        model_t::forward_one(tensor_t<float> const&, bool) [11]
                0.07    0.08    1000/2000        model_t::apply(tensor_t<float> const&) const [12]
[7]     46.7    0.13    0.15    2000         fc_layer_t::activate(tensor_t<float> const&) [7]
                0.02    0.04 15680000/15690000     tensor_t<float>::operator()(int, int, int) const [16]
                0.01    0.03 15700000/47067840     tensor_t<float>::operator()(int, int, int) [13]
                0.04    0.00 15680000/31360000     fc_layer_t::map(point_t) [15]
                0.01    0.00 15680000/31363914     point_t::point_t(int, int, int) [18]
                0.00    0.00   20000/30000       std::vector<float, std::allocator<float> >::operator[](unsigned long) [1013]
                0.00    0.00   20000/20000       fc_layer_t::activator_function(float) [1016]
                0.00    0.00    2000/2000        layer_t::copy_input(tensor_t<float> const&) [1039]
```

Notice anything interesting?  First, this function (and the functions
it calls), takes 46% of execution time -- a juicy target for
optimization!  Second, several of the short, frequently-called
functions are called many, many times by this function.  So, even
though `tensor_t<float>::operator()(int, int, int) const` and
`fc_layer_t::activate` appeared to be indpendent in the flat profile,
their performance and contribution to runtime are actually deeply
intertwined.

### What's the Compiler Doing?

To learn more about what's going on, let's take a look at what the compiler is
doing with the code.  Here's the source code for `fc_layer_t::activate`:

```
void activate(const tensor_t<float>& in ) {
	copy_input(in);
	for ( int n = 0; n < out.size.x; n++ )
	{
		float inputv = 0;

		for ( int i = 0; i < in.size.x; i++ )
			for ( int j = 0; j < in.size.y; j++ )
				for ( int z = 0; z < in.size.z; z++ )
				{
					int m = map( { i, j, z } );
					inputv += in( i, j, z ) * weights( m, n, 0 );
				}

		input[n] = inputv;

		out( n, 0, 0 ) = activator_function( inputv );
	}
}
```

You'll become intimately aquainted with this code in the next lab.
For now, we can just observe a few key things about this code (Since
it's C++, function invocations can be hard to spot):

1. It's a bunch of nested loops.

2. `tensor_t<float>::operator()(int, int, int) const` is getting invoked by `in(i ,j ,z)`, which does read-only multi-dimension access on a the tensor `in`.

3. `tensor_t<float>::operator()(int, int, int)` is getting invoked by `out(n,0,0)`, which suppport assignment to the tensor `out`.

4. `point_t::point_t(int, int, int)` is invoked by `{ i, j, z }`, which constructs a `point_t` to pass to `fc_layer_t::map(point_t)`.

That accounts for all the frequently-called functions.

Now we know why those functions are getting so much, but what does that look
like to processor?  For that, we need to see the assembly that the compiler is
generating.

You should have a `code.s` in your local directory (if you don't do `runlab
--devel` to regenerate it).  It contains the assembly for everything in
`code.cpp`, which includes `fc_layer_t::activate()`.  The file is really
long -- ~10,000 lines -- so we'll have to search for what we need.

Try searching for `fc_layer_t::activate` -- Any luck?  I thought not.  Try
searching for `activate` instead.  You should find something like this:

```
.LFE2976:
        .size   _ZN10fc_layer_t3mapE7point_t, .-_ZN10fc_layer_t3mapE7point_t
        .section        .text._ZN10fc_layer_t8activateERK8tensor_tIfE,"axG",@progbits,_ZN10fc_layer_t8activateERK8tensor_tIfE,comdat
        .align 2
        .weak   _ZN10fc_layer_t8activateERK8tensor_tIfE
        .type   _ZN10fc_layer_t8activateERK8tensor_tIfE, @function
_ZN10fc_layer_t8activateERK8tensor_tIfE:
.LFB2977:
        .cfi_startproc
        pushq   %rbp
        .cfi_def_cfa_offset 16
        .cfi_offset 6, -16
```

It turns out that `_ZN10fc_layer_t8activateERK8tensor_tIfE` is the
"mangled" name for `fc_layer_t::activate(tensor_t<float> const&)`.
Mangling is necessary for C++ for a bunch of reasons, notably
templates and function/operator overloading, and because the assembler
and linker don't like weird punctuation in symbol names.

The exact algorithm for mangling is compiler-dependent.  Fortunately,
`g++` provides a demangling utility called `c++filt` that will clean
this up for us.  Open the file again with

```
c++filt < code.s | less
```

Or created a filtered version with 

```
c++filt < code.s > code-demangled.s
```

Commit the resulting code as `outputs/baseline-demangled.s` and open it. You will have to use -f to force adding a .s file. Please make sure you only add these files.

Now searching for `fc_layer_t::activate` will give you:

```
        .weak   fc_layer_t::activate(tensor_t<float> const&)
        .type   fc_layer_t::activate(tensor_t<float> const&), @function
fc_layer_t::activate(tensor_t<float> const&):
.LFB2977:
        .cfi_startproc
        pushq   %rbp
        .cfi_def_cfa_offset 16
        .cfi_offset 6, -16
        movq    %rsp, %rbp
        .cfi_def_cfa_register 6
        pushq   %rbx
        subq    $88, %rsp
        .cfi_offset 3, -24
1:      call    mcount
        movq    %rdi, -72(%rbp)
```

There's a lot here, but you don't need to understand it in detail to get some idea of what's going.  Here's some key facts that will make the assembly more comprehensible:

1.  Lines that end in `:` are labels -- just named places in the code (e.g., the beginnings of functions)

2.  Labels that begin with `.` are generated by the compiler and are used as branch targets.

3.  Non-labels that start with `.` are assembler directives, not instructions.  You can generally ignore them.

4.  `call mcount` is part of `gprof`, those calls won't be there if you compiled without the profiler.

If you scroll down a bit until you find the assembly code for the body of the inner loop. You'll know it when you find because it'll have `call` instructions to invoke the frequently-called functions.  Here it is:


``` 
        movq    -80(%rbp), %rax
        movl    8(%rax), %eax
        cmpl    %eax, -44(%rbp)
        jge     .L146
        movl    -44(%rbp), %ecx
        movl    -48(%rbp), %edx
        movl    -52(%rbp), %esi
        leaq    -36(%rbp), %rax
        movq    %rax, %rdi
        call    point_t::point_t(int, int, int)
        movq    -36(%rbp), %rcx
        movl    -28(%rbp), %edx
        movq    -72(%rbp), %rax
        movq    %rcx, %rsi
        movq    %rax, %rdi
        call    fc_layer_t::map(point_t)
        movl    %eax, -40(%rbp)
        movl    -44(%rbp), %ecx
        movl    -48(%rbp), %edx
        movl    -52(%rbp), %esi
        movq    -80(%rbp), %rax
        movq    %rax, %rdi
        call    tensor_t<float>::operator()(int, int, int) const
        movss   (%rax), %xmm2
        movss   %xmm2, -84(%rbp)
        movq    -72(%rbp), %rax
        leaq    104(%rax), %rdi
        movl    -60(%rbp), %edx
        movl    -40(%rbp), %eax
        movl    $0, %ecx
        movl    %eax, %esi
        call    tensor_t<float>::operator()(int, int, int)
        movss   (%rax), %xmm0
        mulss   -84(%rbp), %xmm0
        movss   -56(%rbp), %xmm1
        addss   %xmm1, %xmm0
        movss   %xmm0, -56(%rbp)
        addl    $1, -44(%rbp)
        jmp     .L147
```

There are few things to notice about this code:

1.  There are a lot of `mov` instructions with  '(' and ')' in the arguments.  In x86 assembly, these are memory access instructions.

2.  There are quite few `call` instructions.

3.  There are a several instructions accessing `xmm` registers.  These are floating point operations.

One of those calls is to `tensor_t<float>::operator()(int, int, int)`.
Take a look at the implementation in the source and compare it to the
assembly (15 instructions):

```
tensor_t<float>::operator()(int, int, int):
.LFB3402:
        .cfi_startproc
        pushq   %rbp
        .cfi_def_cfa_offset 16
        .cfi_offset 6, -16
        movq    %rsp, %rbp
        .cfi_def_cfa_register 6
        subq    $32, %rsp
        movq    %rdi, -8(%rbp)
        movl    %esi, -12(%rbp)
        movl    %edx, -16(%rbp)
        movl    %ecx, -20(%rbp)
        movl    -20(%rbp), %ecx
        movl    -16(%rbp), %edx
        movl    -12(%rbp), %esi
        movq    -8(%rbp), %rax
        movq    %rax, %rdi
        call    tensor_t<float>::get(int, int, int)
        leave
        .cfi_def_cfa 7, 8
        ret
```

Note the large number of memory operations (i.e., instructions with parantheses).

This function calls `tensor_t<float>::get(int, int, int)` which looks like this (27 instructions):

```
tensor_t<float>::get(int, int, int):
.LFB3513:
        .cfi_startproc
        pushq   %rbp
        .cfi_def_cfa_offset 16
        .cfi_offset 6, -16
        movq    %rsp, %rbp
        .cfi_def_cfa_register 6
        subq    $24, %rsp
        movq    %rdi, -8(%rbp)
        movl    %esi, -12(%rbp)
        movl    %edx, -16(%rbp)
        movl    %ecx, -20(%rbp)
        movq    -8(%rbp), %rax
        movq    16(%rax), %rdx
        movq    -8(%rbp), %rax
        movl    (%rax), %ecx
        movq    -8(%rbp), %rax
        movl    4(%rax), %eax
        imull   %ecx, %eax
        imull   -20(%rbp), %eax
        movl    %eax, %ecx
        movq    -8(%rbp), %rax
        movl    (%rax), %eax
        imull   -16(%rbp), %eax
        addl    %eax, %ecx
        movl    -12(%rbp), %eax
        addl    %ecx, %eax
        cltq
        salq    $2, %rax
        addq    %rdx, %rax
        leave
        .cfi_def_cfa 7, 8
        ret
```

Again, lots of memory access.  So the expression `in( i, j, z )` results in the
execution of over 42 instructions, about half of which are memory accesses.

Check the source code for `tensor_t<float>::get(int, int, int)` and make sure
you understand it.  The heart of `in( i, j, z )` is a handful of multiplies and
additions.  If you look carefully, you can see them near the end of the listing
above.  Most of the remaining instructions are just overhead related to calling
the functions and moving data on and off the stack.

### Looking at the Performance Counters

The analysis of the profiling data and the compiler output suggests that

1. Small, frequently called functions are a performance problem, and

2. The compiler's baseline implementation of these functions and how they are called are inefficient.

We can bolster this analysis further by looking at the hardware
performance counters while our test code is running.  You've already collected
this data in `outputs/baseline-inst_mix.csv`.  Here's an example:

GPROF|OPTIMIZE|DEVEL_MODE|STATS   |dataset |training_inputs_count|nJ      |runtime|insts   |mem_ops |branches|uncond_branches|
-----|--------|----------|--------|--------|---------------------|--------|-------|--------|--------|--------|---------------|
no   |-O0     |          |INST_MIX|mnist   |2e+02                |1.73e+09|0.135  |8.81e+08|6.48e+08|8.38e+07|6.93e+07       |
no   |-O0     |          |INST_MIX|emnist  |1.5e+02              |4.79e+09|0.349  |2.38e+09|1.75e+09|2.26e+08|1.87e+08       |

For your data (and all the data sets), calculate the percentage
of total instructions that are memory operations, branches, and unconditional
branches.

* mnist
* emnist
* cifar10
* cifar100
* imagenet 


Also calculate the number of memory operations, branches, and unconditional
branches *per training input*.

The output from your test contains this line and others like it: 

```
  * Total 1: 37 kB
```

That gives the total memory required for the model.  Compare this
number to the number of memory operations performed per training
input.  

### Asking the Compiler to Do More

Clearly, this code is doing more work than it needs to, but how should we fix it?

The are two main sources of inefficiency in the code above.

1. There are a lot of memory accesses

2. Calling small functions (that sometimes, in turn, call small functions) leads to inefficiency.

Both of these are very common performance problems.  The second is
especially common in object-oriented code, since it's goal is, in
large measure, to facilitate code reuse, which tends to lead to lots
of function calls to small functions.  (Your CSE11 or 8a/8b professors
added fuel to the fire by suggesting your break programs up into
small, easy-to-understand pieces.)

Fixing these problems in the source code is probably possible, but the changes
required to the code would be complex, error-prone, and probalby result in code
that was unmaintainable.

Instead, we can just ask the compiler to fix it!

Set `OPTIMIZE=-O3` and leave `gprof` enabled in `config.env`, commit and resubmit
to the autograder.

Commit the resulting csv files as `outputs/optimized-gprof-pe.csv` and  `outputs/optimized-gprof-inst_mix.csv`. You will have to use -f to force adding a .csv file. Please make sure you only add these files.

Demangle `code.s` and take a look at the inner loop body of the new version of `fc_layer_t::activate`:

```
.L278:
        testl   %edi, %edi
        jle     .L276
        movq    16(%r15), %rcx
        movslq  %r10d, %rax
        movl    8(%rbx), %edx
        addq    %r11, %rax
        movl    12(%rbx), %esi
        leaq    (%rcx,%rax,4), %rcx
        movl    104(%rbx), %eax
        imull   %edx, %esi
        imull   %r14d, %eax
        imull   %r9d, %edx
        movslq  %esi, %rsi
        cltq
        salq    $2, %rsi
        addq    %r11, %rax
        movslq  %edx, %rdx
        addq    %rax, %rdx
        movq    120(%rbx), %rax
        leaq    (%rax,%rdx,4), %rdx
        xorl    %eax, %eax
```

It looks a great deal better!  Many fewer memory accesses and no function
calls!  In fact, the whole loop body is just 21 instructions, about half the
number we needed previously for `in( i, j, z )`.

Further, the those function calls are to `tensor_t<float>::get(int,
int, int) const`, instead of one of the intermediate
`tensor_t<float>::operator()()` functions.

### Measuring Actual Performance

So far, we have been running the code with the profiler enabled, but `gprof`
adds some overhead.  To measure the real performance, disable `gprof` in
`config.env` and resubmit.

Save the resulting csv file as `outputs/optimized{-pe,-inst_mix}.csv`.

### Reasoning About Performance

Pick the functions that accounted for the largest fraction of
execution time in your optimized gprof output.  We will call this your
"hot function".

Modify your `config.env` to re-enable gprof and run just one workload.
Submit it, and save the resulting gprof output.  Repeat this with the
other workloads.

While that's running, examine the the code for your function in the
Canela source code.  What is the O() (i.e., Big-O) complexity of this
function?  In your O() expression use 'm' as input size and 'n' as the
output size.  The input size is the total number of inputs.  For our
datasets, this is product of the width, height, and depth of the
image.  Depth is 1 for grayscale and 3 for RGB.  'n' is the number of
categories.  The necessary information is given in the table above.

Using your O() expression estimate the runtime for each dataset
relative to mnist.  For instance:

| dataset  | m    | n   | relative-m     | relative-n |
|----------|------|-----|----------------|------------|
| mnist    | 784  | 10  | 1 = 784/784    | 1 = 10/10  |
| cifar100 | 3072 | 100 | 3.9 = 3072/784 | 10 = 100/10|

You can then estimate execution time of `cifar100` *relative to*
`mnist` using the relative value.  For instance, if you estimate that
your hot functions is O(n*m), then the relative execution time for
`cifar100` is 3.9*10=39.

Follow the instructions in the lab write up to analyze this data.

### Changing the Clock Rate and Measuring Power

First, we no longer need to use gprof, so lets comment it out.
```
#GPROF=yes
```

If inside `pe.csv` you'll see a `MHz` column.  It shows the clock rate
your experiments have been running at.  You can control the clock rate
with the `--MHz` option in `CMD_LINE_ARGS` option in your
`config.env`.  For now, just add `--MHz 900`.

To speed things up, we'll just do one data set: `cifar100`.  Adjust
your `config.env` accordingly.

We will also measure energy consumption.  To do this, you'll need to
add a new performance counter.  Take a look in
`papi_native_avial.txt`.  It lists all of the performance counters
available on our processor.  As you can see, there are many of them.

The counter we want is part of Inte's Running Average Power Limt
(RAPL) interface, and it measures the number of nanojoules used by the
processor package (which includes the whole chip -- all the cores and
peripheral circuits).  It is called `rapl:::PACKAGE_ENERGY:PACKAGE0`.

To measure it add this to your `CMD_LINE_ARGS` in `config.env`:

```
--stat nJ=rapl:::PACKAGE_ENERGY:PACKAGE0
```

Let's break that down:

* `--stat` tells your executable to measure a performance counter.
* `nJ=` sets the column label that will appear in the CSV file.
* `rapl:::PACKAGE_ENERGY:PACKAGE0` is the counter you want to measure.

We will also measure power.  There is no performance counter for
power, but we can compute it from energy and execution time.  To
compute power (in Watts), add this to `CMD_LINE_ARGS` (Why did I add
`/1e9`?):

```
--calc W=nJ/runtime/1e9
```

* `--calc` says you are adding a computed column to the CSV file.

* `W=` is the name of the column

* `nj/runtime/1e9` is the expression.  It can be any valid Python expression.  You can use column names as variables. 

You should end up with something in your `config.env` like:

```
CMD_LINE_ARGS="--dataset cifar100 --MHz 900 --stat nJ=rapl:::PACKAGE_ENERGY:PACKAGE0 --calc W=nJ/runtime/1e9"
```

After you've made all these changes, commit and submit.

Check `pe.csv` to verify that the clock rate changed.  Copy (but you don't need to
commit) `pe.csv` to something like `pe-900.csv`.  You'll have
something like this (some columns and rows have been removed):

| nJ     |inst_count|runtime|MHz    |cycles |IPC | W  | 
|--------|----------|-------|-------|-------|----|----|
|2.25e+09|8.27e+08  |0.456  |9e+02  |4.1e+08|2.02| 4.9|

Here's what the fields mean:

* `nJ` -- nanojoules consumed by the entire processor die.
* `inst_count` -- Instruction count
* `runtime` -- execution time (or latency)
* `MHz` -- The value of the clock rate parameter you passed to the tool.
* `cycles` -- number of actual (i.e., not reference) clock cycles.
* `IPC` -- Instructions per cycle.
* 'W' -- How many Watts the processor die is consuming.

Add `--MHz 1000`,  `--MHz 1100`,  up to `--MHz 2000` and rerun.

Combine the resulting `pe.csv` files to `outputs/pe-clockrate.csv` and commit `outputs/pe-clockrate.csv`. You will have to use -f to force adding a .csv file. Please make sure you only add these files.

Load `outputs/pe-clockrate.csv` into a spreadsheet. (**TIP**: The
easiest thing to do is copy it's contents and paste it into a Google
Sheet.  The select`"Data->Split Text into columns`).

Note that, as expected, `inst_count`, `cycles`, and `IPC` don't change
when we changed the clock speed.

## Turn in Your Work

Submit your code repo and completed worksheet via their respective
assignments on gradescope.  You can submit the code portion as many
times as you like, but the worksheet only once.



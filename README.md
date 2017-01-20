# IRacket

IRacket is a Racket kernel for
[IPython](https://ipython.org/)/[Jupyter](http://jupyter.org/). IRacket enables
interactive notebook-style programming with Racket. This package also includes
Racket bindings for the [C3.js](http://c3js.org/) charting library.

This package includes a number of sample notebooks that demonstrate IRacket and
the [Gamble](https://github.com/rmculpepper/gamble) probabilistic programming
language.

# Installation

## Requirements

* [Racket v6](http://racket-lang.org)
* These Racket packages installed (via `raco pkg install`):
    * [`git://github.com/greghendershott/sha.git`](https://github.com/greghendershott/sha)
    * [`git://github.com/mordae/racket-libuuid.git`](https://github.com/mordae/racket-libuuid)
    * [`git://github.com/tgiannak/racket-zeromq.git#wait-on-fd`](https://github.com/tgiannak/racket-zeromq#wait-on-fd)
    * [`git://github.com/rmculpepper/gamble.git`](https://github.com/rmculpepper/gamble)
* [ZeroMQ](http://zeromq.org)
* [IPython version 3.2.1](https://pypi.python.org/pypi/ipython/3.2.1)

## Installation steps

1. Clone this repository.
2. Run the `installation.sh` script from the current directory.
```bash
./installation.sh
```

If you previously installed IRacket, answer "n" when it asks about
installing C3 integration.  Otherwise say "y" or hit enter.

### Manual installation instructions

1. Clone this repository.
2. Run the following from the root of this repository
```bash
mkdir -p $(ipython locate)/kernels/racket/
cp ./static/kernel.json $(ipython locate)/kernels/racket/kernel.json
```
3. Adjust the copied `kernel.json` to refer to iracket in the path of this
   repository in place of `IRACKET_SRC_DIR`.
4. If `racket` isn't on your path, adjust the copied `kernel.json` to refer to
   the absolute path of `racket`.

#### C3 Integration (Charts)

_Note that the front-end integration for C3 will eventually be moved into its
own repository._

If you use a non-default profile, set `IPYTHON_PROFILE` to the name of that
profile, then run the following:

```bash
IPYTHON_PROFILE=
IPYTHON_PROFILE_STATIC=$(ipython locate profile $IPYTHON_PROFILE)/static
cp ./static/ic3.js $(ipython locate)/nbextensions/ic3.js
cat ./static/custom.js >> $IPYTHON_PROFILE_STATIC/custom/custom.js
curl -L https://github.com/mbostock/d3/raw/v3.5.5/d3.min.js \
    > $IPYTHON_PROFILE_STATIC/d3.js
curl -L https://github.com/masayuki0812/c3/raw/0.4.10/c3.min.js \
    > $IPYTHON_PROFILE_STATIC/c3.js
curl -L https://github.com/masayuki0812/c3/raw/0.4.10/c3.min.css \
    > $IPYTHON_PROFILE_STATIC/c3.css
```

This script will
* copy the nbextension for C3 into your IPython nbextensions folder,
* append the code for loading the extension to your IPython profile's
  custom.js, and
* add the required D3 and C3 code to your IPython profile's static resources
  folder.

## Displaying C3 charts from IRacket

To display C3 charts, evaluate a `cons` cell whose `car` is the symbol
`'c3-data` and whose `cdr` is a `jsexpr` (from the `json` package) of the data
structure to pass to C3's `generate` function.

Example:
```racket
(cons 'c3-data
      (hasheq 'data
              (hasheq 'columns
                      (list (list "data1" 30 200 100 400 150 250)
                            (list "data2" 50 20 10 40 15 25)))))
```

For more information on C3's data format, see
[the C3 reference](http://c3js.org/reference.html), or for examples see
[the C3 examples page](http://c3js.org/examples.html). For information
on how to write JSON in Racket, see the Racket
[JSON package documentation](http://docs.racket-lang.org/json/index.html).

Note that certain C3 features are not currently usable because they rely
on using Javascript functions, which are not expressible in JSON.


# Using the kernel

Run the IPython notebook server as you usually do, e.g.
```bash
ipython notebook
```
and create a new notebook with the Racket kernel.

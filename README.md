[![Build Status](https://travis-ci.org/jiosue/QUBOVert.svg?branch=master)](https://travis-ci.org/jiosue/QUBOVert)

# QUBOVert

## Convert common problems to QUBO form.

Add tests for any functionality that you add. Make sure you run `python -m pytest` before committing anything to ensure that the build passes. When you push changes to the master branch, travis-ci will automatically check to see if all the tests pass. I'm not sure if it will check if you push to another branch, but I think it does.


So far we have just implemented the formulations from [Lucas]. For the log trick, we usually need a constraint like $$\sum_{i} x_i \geq 1$$. In order to enforce this constraint, we add a penalty to the QUBO of the form $$1 - \sum_i x_i + \sum_i < j x_i x_j$$ (the idea comes from [Glover et al]).


Use Python's `help` function! I have very descriptive doc strings on all the functions and classes. To install from source:

```shell
git clone https://github.com/jiosue/qubovert.git
cd qubovert
pip install -r requirements.txt
pip install -e .
```

Then you can use it in Python with

```python
import qubovert

# get info
help(qubovert)
help(qubovert.utils)
```





# Technical details on the conversions

## Set Cover


Following the notation from [Lucas], we want to minimize

$$H_B = B \sum_{i=1}^N x_i$$.

To enforce the constraints, following the notation of [Lucas], without using the log trick we also want to minimize

$$H_A = A\sum_{\alpha \in U} \left[\left(1-\sum_{m=1}^M x_{\alpha, m}  \right)^2 + \left(\sum_{m=1}^M m x_{\alpha, m} - \sum_{i: \alpha \in V_i} x_i \right)^2 \right]$$.

Let $$d = \lfloor \log_2 M \rfloor + 1$$. Using the log trick, we want to minimize

$$H_A = A\sum_{\alpha \in U} \left[\left(1-\sum_{m=0}^{d} x_{\alpha, m} + \sum_{m=0}^{d} \sum_{m'=m+1}^{d} x_{\alpha, m} x_{\alpha, m'} \right) + \left(\sum_{m=0}^{d} 2^m x_{\alpha, m} - \sum_{i: \alpha \in V_i} x_i \right)^2 \right]$$.

Where the first constraint enforces that $$\sum_m 2^m x_{\alpha, m} \geq 1$$ (the idea comes from [Glover et al]).


To convert the indices to single numbers, we have that $$i\to i$$ for each $$x_i$$, and $$x_{\alpha, m} \to x_j$$, where $$j(\alpha, m) = N + nm + f(\alpha)$$ for the log trick and $$j(\alpha, m) = N + n(m-1) + f(\alpha)$$ otherwise. Where $$f(\alpha)$$ sends $$\alpha$$ to an index.

Also we have defined

$$M = \max_{\alpha \in U}\sum_{i=1}^N \delta_{\alpha \in V_i}$$



# References

[Lucas] Andrew Lucas. Ising formulations of many np problems. Frontiers in Physics, 2:5, 2014.

[Glover et al]  Fred Glover, Gary Kochenberger, and Yu Du. A tutorial on formulating and using qubo models. arXiv:1811.11538v5, 2019.
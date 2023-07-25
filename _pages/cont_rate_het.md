---
layout: single
permalink: /cont_rate_het/
title: "Modeling variation in rates of continuous trait evolution"
author_profile: true
classes: wide
---

## Overview

Rates of trait evolution vary markedly across the tree of life, from adaptive radiations to lineages of "living fossils", but our tools for characterizing and analyzing this rate heterogeneity are still lacking in some cases. My current major research focus is developing novel models, approaches, and algorithms to address some of these gaps in the case of modeling continuous trait evolution.
{: style="text-align: justify; font-size:0.75em;"}

## Evolving rates

Empirical evidence and theory both suggest that the rates of trait evolution are influenced by a vast, interconnected web of various life history and environmental factors. If we assume these factors all change gradually over evolutionary time and usually have subtle effects on rates, it becomes reasonable to actually think of the rate of trait evolution as a kind of trait in and of itself--one we can model the evolution of! For simplicity (and in line with analogous methods for time calibrating phylogenies), let's assume that rates evolve via a geometric Brownian Motion process characterized by a trend parameter (determining whether rates tend to decrease or increase over time) and rate variance parameter (determining the magnitude of random changes in rates over time).
{: style="text-align: justify; font-size:0.75em;"}

I developed a Bayesian implementation of this model--which I dubbed "evolving rates" or "evorates" for short--using the probablistic programming language [Stan](https://mc-stan.org/). The implementation is available through [my R package up on GitHub](https://github.com/bstaggmartin/evorates/), and can be used to infer trend and rate variance parameters from empirical phylogenies and associated continuous trait data, as well as branchwise rates--average rates of trait evolution along each branch in the phylogeny. Notably, the current implementation supports multiple trait values per tip, within-tip variance in trait values (fixed beforehand and/or estimated during model fitting), missing trait data, non-ultrametric trees, and trait values assigned to internal nodes. The only catch is that it doesn't yet support multivariate trait data!
{: style="text-align: justify; font-size:0.75em;"}

Check out the [associated publication](https://doi.org/10.1093/sysbio/syac068) for further information!
{: style="text-align: justify; font-size:0.75em;"}

## Continuous stochastic character mapping

A central aim of macroevolutionary biology is deciphering how environmental and life history factors affect the tempo and mode of evolutionay processes. For example, do lineages of larger organisms generally exhibit slower or faster rates of phenotypic evolution? Such hypotheses are often straight-forward to test using phylogenetic comparative methods provided the evolutionary history of the explanatory factor (e.g., body size) is known, though this is rarely the case in practice. A common workaround is to instead repeatedly simulate the factor's history and use these simulations, termed stochastic character maps or "simmaps" for short, in place of the true (but unknowable) history. Unfortunately, current implementations of simmaping only work for discrete factors, which is shame because many factors commonly hypothesized to affect evolutionary processes are continuous (e.g., body size, temperature, generation time).
{: style="text-align: justify; font-size:0.75em;"}

To expand our hypothesis-testing capabilities within the simmaping framework, I developed an algorithm for simmaping continous variables ("contsimmaps") under a very flexible class of Brownian Motion models (with support for Ornstein-Uhlenbeck models planned for the future). The implementation is already available through [my R package up on GitHub](https://github.com/bstaggmartin/contsimmap/), though it lacks documentation and some convenience features. Similarly to my _evorates_ package, the current implementation supports multiple values per tips, within-tip variance, missing data, non-ultrametric trees, and values assigned to internal nodes. Additionally, this one actually works with multivariate data! It also supports so-called "multiregime" Brownian Motion models, whereby parameters (trends, rates, correlations, etc.) of Brownian Motion models vary according to discrete regimes mapped onto the tree via normal, discrete simmaps as implemented in the [_phytools_](http://blog.phytools.org/) package.
{: style="text-align: justify; font-size:0.75em;"}

I am particularly interested in using contsimmaps to estimate relationships between continuous factors and rates of continuous trait evolution. Initial simulation studies indicate that the pipeline works rather well, and I'm currently in the process of writing up a manuscript both introducing contsimmaps as a whole and demonstrating this particular application. Stay tuned for further updates!
{: style="text-align: justify; font-size:0.75em;"}

## State-dependent character evolution

State-dependent speciation and extinction (SSE) models are a major boon to research on lineage diversification dynamics, allowing researchers to efficiently test whether speciation/extinction rates vary according to some discrete trait (consisting of multiple "states"). SSE models are powerful because they combine discrete trait evolution and lineage diversification models into a unified mathematical framework. This additionally allows SSE models to infer so-called ["hidden states"](https://doi.org/10.1093/sysbio/syw022)--unobserved discrete "traits" that leave a detectable impact on lineage diversification dynamics and/or the evolution of observed discrete traits. Hidden states have become a popular tool for phylogenetic comparative data exploration (["phylogenetic natural history"](https://doi.org/10.1093/sysbio/syy031)) and [creating better null models for comparative hypothesis testing](https://doi.org/10.1093/sysbio/syac066).
{: style="text-align: justify; font-size:0.75em;"}

Unfortunately, we really don't have a similar mathematical framework for uniting discrete and continuous trait evolution models. Most current implementations of state-dependent character evolution (SCE) models (whereby, analogously to SSE, aspects of continuous trait evolution dynamics--like rates--vary according to some discrete trait) work by explicitly simulating evolutionary histories of discrete traits via stochastic character mapping (see above), then fitting continuous trait evolution models assuming these simulated histories are true. Depending on the implementation, this approach ranges from inefficient at best to a crude approximation at worst while also making inference of hidden states extremely difficult (notably, however, [a recently-introduced method called hOUwie](https://doi.org/10.1093/evolut/qpad002) cleverly circumvents many of the aforementioned issues by using the continuous trait data to dynamically adjust simulated discrete trait histories).
{: style="text-align: justify; font-size:0.75em;"}

For this project, I aimed to develop an efficient and practical way to calculate exactly how discrete and continuous traits simultaneously change under arbitrary SCE models, obviating the need to simulate discrete trait histories at all when fitting SCE models to empirical data. I managed to come up with a relatively fast and nearly exact solution by discretizing continuous trait space into a large number of "bins" (usually 1024-4096), keeping track of probabilities in each bin, and computing how these probabilities change over time with some useful math tricks (namely, fast Fourier transforms and matrix exponetials). Ultimately, this new framework allows researchers to quickly fit SCE models to infer how discrete traits (including hidden states) impact continuous trait evolution dynamics--including rates! Excitingly, the framework is generalizable to all Levy process-based models of continuous trait evolution--this includes more "drift-y" processes like Brownian Motion and its [jumpy/pulsed cousins](https://doi.org/10.1093/sysbio/sys086), but unfortunately excludes more "adaptive-y" processes like Ornstein-Uhlenbeck.
{: style="text-align: justify; font-size:0.75em;"}

I have a working prototype of this framework available through [my R package up on GitHub](https://github.com/bstaggmartin/sce/). Keep in mind that the package is not nearly complete nor documented--so far, it really only implements a function for fitting Brownian Motion models with state-dependent rates (as well as reconstructing ancestral states, which is pretty neat). Stay tuned for future updates and a preprint!
{: style="text-align: justify; font-size:0.75em;"}
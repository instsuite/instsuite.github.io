---
layout: page
title: Home
order: 10
---
# Welcome to the InstAL project pages

InstAL is the Institutional Action Language.

The InstAL project provides tools for the specification, verification and monitoring of normative frameworks written in InstAL.

InstAL works by translating institutional specifications into Answer Set Prolog (AnsProlog), then using an answer set solver (we use <a href="http://potassco.org/">clingo</a>) to generate answer sets, subject to the constraints of a specified number of time steps and whatever constraints - positive or negative - the client wishes to express about the properties of the answer set.

InstAL is provided as a set of command-line tools written in Python. You can create a local installation of the InstAL compiler, answer set solver and visualization tools and work directly with those. Alternatively, use command-line tools to create InstAL services, using the InstAL-REST framework. 

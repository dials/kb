# Roadmap

## Introduction

In team meeting 2023-05-02 the need for a clear roadmap for where we want to take DIALS was identified, at least in terms of starting to tackle the backlog of issues and work out where we want to take the project.

## Proposal

Discuss the roadmap through a series of PRs against the `roadmap` branch  with the conclusion being a functional plan. This needs to identify the path we take and the goals we want to achieve.

## Immediate issues

Propose the following issues to address:

- memory use in `dials.integrate`

This is a rather egregious and evergreen issue, cast into sharp relief every time I teach at a workshop.

## Possible solutions

`dials.integrate` memory footprint:

- revisit structure of `dials.integrate`
- consider reimplementation against existing API, perhaps as a straight binary

Could form part of a "little dials" project, perhaps?

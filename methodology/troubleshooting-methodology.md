# Troubleshooting Methodology

## Purpose

This document records the structured approach I use when investigating technical problems.

The objective is to avoid making assumptions based on individual errors or symptoms and instead work progressively from observation toward an evidence-supported conclusion.

## 1. Define the Problem

Start by describing what is actually happening rather than immediately attempting to explain why.

Record:

- The observed symptom
- When it occurred
- What was happening immediately beforehand
- Whether the behaviour is reproducible
- What remains functional and what does not

Separating observation from interpretation helps prevent an early assumption from directing the entire investigation.

## 2. Establish a Baseline

Before changing anything, establish what normal behaviour looks like.

Depending on the problem this may include:

- CPU utilisation
- Memory utilisation
- Running processes
- Network activity
- Hardware state
- Application behaviour
- Relevant system logs

This provides something against which abnormal behaviour can be compared.

## 3. Gather Evidence

Collect information relevant to the time and context of the problem.

Examples include:

- System and application logs
- Process information
- Hardware or driver messages
- Configuration
- Error output
- User-observed behaviour

Where possible, narrow evidence to the period immediately surrounding the event.

## 4. Separate Signal From Noise

The presence of an error does not establish that it caused the problem.

I look for:

- Events occurring at the same time as the symptom
- Repeated patterns
- Events that consistently precede a failure
- Changes from the established baseline
- Evidence that supports or contradicts a hypothesis

Unrelated warnings should not be promoted to root causes simply because they appear unusual.

## 5. Form a Hypothesis

Use the available evidence to identify a possible explanation.

A useful hypothesis should be testable.

For example:

> If component X is contributing to the failure, changing or isolating X should produce an observable change in behaviour.

At this stage the hypothesis remains a possibility, not a conclusion.

## 6. Change One Variable

Where practical, change one variable at a time.

This makes it easier to determine whether the change affected the result.

Changing multiple components simultaneously may make a problem disappear while providing little evidence about what actually caused it.

## 7. Attempt Reproduction

A reproducible failure provides an opportunity to compare:

- Actions immediately before the problem
- System behaviour
- Log events
- Changes made since the previous test

Reproduction can therefore be more useful than a temporary workaround.

## 8. Evaluate the Result

After each test:

- Did the behaviour change?
- Does the result support the hypothesis?
- Does it contradict it?
- Was the test conclusive?
- What should be investigated next?

An improvement after a change is evidence, but does not automatically establish causation.

## 9. Confirm the Resolution

A problem should not be considered resolved simply because it has stopped occurring temporarily.

Where possible:

1. Apply the proposed fix.
2. Repeat the conditions that previously caused the problem.
3. Confirm expected behaviour.
4. Review relevant evidence.
5. Continue observing for recurrence.

## Working Model

Observe
   ↓
Establish baseline
   ↓
Gather evidence
   ↓
Identify patterns
   ↓
Form hypothesis
   ↓
Change one variable
   ↓
Test / reproduce
   ↓
Evaluate
   ↓
Confirm or revise
   ↓
Document

## Current Principle

**Observation → evidence → hypothesis → test → conclusion**

I will continue refining this methodology as I gain experience investigating different systems and technical environments.

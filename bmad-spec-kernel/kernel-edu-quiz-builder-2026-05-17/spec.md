# Kernel: Formative Quiz Builder from Lesson Plan

## Problem

Classroom teachers need short formative-assessment quizzes that match the specific objectives of a single lesson, but hand-authoring questions during planning time is slow, and generic question banks rarely align to the day's content or grade band.

## Capabilities

- Accept a lesson plan as pasted text or uploaded document.
- Extract the lesson's learning objectives and key vocabulary.
- Generate a quiz of configurable length (3 to 15 items) targeting those objectives.
- Produce multiple item types (multiple choice, short answer, true/false) in one quiz.
- Tag each item with the objective it assesses.
- Flag items the teacher edits or rejects so future generations adapt.
- Export the finished quiz as a printable sheet and a plain-text answer key.

## Constraints

- Must run offline on a school-issued Chromebook with no external API calls during a class period.
- Must complete generation of a 10-item quiz in under 20 seconds on that hardware.
- Must not store student names or student responses.
- Output reading level must be selectable by grade band (K-2, 3-5, 6-8, 9-12).

## Non-goals

- Grading student submissions or tracking scores over time.
- Generating summative or high-stakes assessments.
- Authoring full lesson plans or curriculum from scratch.
- Translating quizzes into other languages.

## Success signal

A teacher produces a usable quiz from a lesson plan in under 5 minutes, with at least 80 percent of generated items kept without edits.

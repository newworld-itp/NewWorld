What is NewWorld?
==================

NewWorld provides an easy and intuitive way to monitor the Layer 2 of your network. It has been developed by three students of the HTL Rennweg in Vienna. It is based on Python and SQLite, but can be comfortabely executed on the command-line. By using NewWorld you can compare the current state of your network with the accepted one.

It is integrated in LazyConf_, and automatically updates without any user-input. This integration enables an even greater monitoring of your network, by monitoring not only the Layer 2 but also Layer 3.

How does NewWorld work?
------------------------

INGInious is based on the concept of tasks (see :ref:`task`). A task is a set of one or more related (sub)questions.
For each task, an infinite number of submissions is allowed, but a user must wait for the result of its current submission before trying a new one.

For simplicity, tasks are grouped by courses (see :ref:`course`).
Usually, an INGInious course has one task per assignment.

A submission is a set of deliverables (chunks of code, files, archives, etc.) that correspond each to one of the (sub)questions of the task.
These files are made available to the *run file* (see :ref:`run_file`), a special script provided by the task.
That script is responsible for providing feedback on the submission by compiling, executing or applying any form of checking and testing to the deliverables.
In its simplest form, the feedback consists of either *success* or *failed*.

This *run file* is run inside a container (precisely, a *grading container*), that completely jails the execution of the script, because even teachers
and assistants are never fully trusted. *Grading containers* are able to start sub-containers, called *student containers*, that runs the scripts
that the students sent with their submission, in another jailed environment.

This separation in two step of the grading is mandatory to ensure a complete security for the server hosting INGInious *and* a complete security of
the grading process, making impossible for the student to interact "badly" with the *run script*.

These containers are created/described by very simple files called Dockerfile. They allow to create containers for anything that runs on Linux.
For details about to create new containers and add new languages to INGInious, see :doc:`teacher_doc/create_container`.
# Atomic Subroutines: A Rationale for Trying Out Them
Fortran 2008 coarray programming with unordered execution segments (user-defined ordering) - Atomic Subroutines: a rationale for trying out them

# Overview

I open this to briefly share some of my personal thoughts and hopes for the development of parallel software in the many-core era. This may also give a rationale for at least trying out parallel programming using Fortran 2008 atomic subroutines and the SYNC MEMORY statement. 

# A Rationale for trying out Fortran 2008 atomic subroutines and SYNC MEMORY

At this time, I am just learning myself that development of the parallel logic codes is quite a different job and should probably not be mixed with the development of the main logic codes. Actually, we may try to answer questions like these:

Will parallel programming alone allow for efficient software development for many-core computers?
Will sequential programmers turn massively into parallel programmers?
Is everyone required to become a parallel programmer to develop codes that run on many-core computers?
(People may try to answer these questions for themselves. If you got three 'yes', I may not be able to convince you yet. For myself, I've got three 'no' yet.)

Probably, we should not expect complete and efficient software development from parallel programming alone. Parallel programming itself can be difficult enough, but mixing the parallel logic codes with the main logic codes may be an extremely daunting experience. The parallel developer should only be responsible for the parallel logic codes, whereas the main logic codes should be developed separately. (Ideally with sequential syntax to allow for development by traditional programmers).

Therefore, a parallel programmer should implement the parallel logic codes but not necessarily the main logic codes of a parallel application: Some parts (objects) of the parallel logic codes will execute on the same coarray images as the main logic codes, but much of the parallel logic codes will require execution on it's own coarray images (i.e. the parallel logic itself will always require a small fraction of the available hardware resources).
Thus, the goals should be (1) to largely separate the main logic codes from the required parallel logic codes and (2) to provide an environment that allows for development of the main logic codes in a sequential syntax style. Such an environment must be provided by both: the parallel programmer as well as the programming language itself, to allow to hide an applications parallel syntax. With Fortran 2008, we have a sequential programming language with only few extensions for parallel programming: coarray objects can be used to encapsulate the parallel logic codes. As such, Fortran is a promising candidate to achieve our goal: we even need only a small subset of it's parallel features that can be encapsulated easily and hidden from the main logic codes.

Keeping the parallel logic codes separated from the main logic codes could allow for a largely separated development of these both parts by distinct developers: while the parallel (framework) logic codes would be developed by parallel developers, the main logic codes could be developed by traditional (sequential) programmers more easily, with two significant changes:<br />
1. To allow for parallel execution and to retain a sequential syntax style for the main logic codes, the programmer may apply an object-based fork/join-like programming style. This may require to avoid object nesting.
2. All data transfer between (non-coarray) objects, remotely as well as locally, must be synchronized using sequential style spin-wait loops.

To achieve that, the parallel programmer must provide derived type coarray objects for the following two purposes:<br />
1. To encapsulate the parallel logic codes and keep them separated from the applications main logic codes. (This works only for a small number of those coarray related language features that can be encapsulated and held separately from the main logic codes). Coarray objects are a simple but yet powerful mechanism to encapsulate the parallel syntax and logics.
2. To provide coarray wrapper (or communicator) objects to be used for the data transfer among the traditional (non-coarray) objects of the main logic codes. (i.e. the non-coarray objects must communicate through PGAS memory). 

With Fortran 2008, things would be much easier if we could develop parallel applications with one execution segment only (i.e. no SYNC MEMORY statement at all) and synchronize with spin-wait loops using the non-atomic data types. As a matter of fact, this really works with some shared memory computer, but HPC hardware may require multiple execution segments (with user-defined ordering at times) and thus, the use of SYNC MEMORY and atomic subroutines. These should only be used from the parallel logic codes. The development of reliable software that way is still an open question: the parallel programmer must be able to implement an easy to use check- and restore-functionality for the segment ordering among images. I am currently on half way with that.



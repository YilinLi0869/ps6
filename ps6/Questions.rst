
Questions
=========

Norm
----

* Look through the code for ``run()`` in ``norm_utils.hpp``.  How are we setting the number of threads for OpenMP to use?
We setting the number of threads for OpenMP by use 
#ifdef _OPENMP
      omp_set_num_threads(nthreads);
#endif
in a for loop that the nthreads starts from 1, ends by n_parts, and increases in powers of two. 
The #ifdef statement means that if we have defined _OPENMP, 
the number of threads for OpenMP will be set to nthreads. 


* Which version of ``norm`` provides the best parallel performance?  How do the results compare to the parallelized versions of ``norm`` from ps5?
norm_block_critical provides the best parallel performance. 
Compare to the parallelized versions of norm from ps5, the norm_block_critical version is a little bit 
better. I think the reason might be that the OpenMP critical directive is better than what we did by 
hand in ps5.


* Which version of ``norm`` provides the best parallel performance for larger problems (i.e., problems at the top end of the default sizes in the drivers or larger)?  How do the results compare to the parallelized versions of ``norm`` from ps5?
norm_block_critical provides the best parallel performance for larger problems. 
Compare to the parallelized versions of norm from ps5, the norm_block_critical version is a little bit 
better. I think the reason might be that the OpenMP critical directive is better than what we did by 
hand in ps5, and it deals with the larger problems better too. 


* Which version of ``norm`` provides the best parallel performance for small problems (i.e., problems smller than the low end of the default sizes in the drivers)?  How do the results compare to the parallelized versions of ``norm`` from ps5?  
norm_block_critical provides the best parallel performance for small problems. 
Compare to the parallelized versions of norm from ps5, the norm_block_critical version is a little bit 
better. I think the reason might be that the OpenMP critical directive is better than what we did by 
hand in ps5, and it deals with the small problems better too. 


Sparse Matrix-Vector Product
----------------------------

* How does ``pmatvec.cpp`` set the number of OpenMP threads to use?
It set the number of OpenMP threads to use as:
#ifdef _OPENMP
    omp_set_num_threads(nthreads);
#endif
in a for loop that the nthreads starts from 1, ends by n_parts, and increases in powers of two. 
The #ifdef statement means that if we have defined _OPENMP, 
the number of threads for OpenMP will be set to nthreads. 


* (For discussion on Piazza.)
What characteristics of a matrix would make it more or less likely to exhibit an error 
if improperly parallelized?  Meaning, if, say, you parallelized ``CSCMatrix::matvec`` with just basic  columnwise partitioning -- there would be potential races with the same locations in ``y`` being read and written by multiple threads.  But what characteristics of the matrix give rise to that kind of problem?  Are there ways to maybe work around / fix that if we knew some things in advance about the (sparse) matrix?
If we use CSCMatrix::matvec as an example, then repeated values of the other indices of the same 
columns will give rise to that kind of problem. However, according to the algorithms that we used, 
the CSCMatrix::t_matvec will not be influenced by that. 
Same for the CSRMatrix. If we use CSRMatrix::matvec, then repeated values of the other indices of 
same rows will give rise to that kind of problem. However, according to the algorithms that we used, 
the CSRMatrix::t_matvec will not be influenced by that. 
Therefore, for CSCMatrx and CSRMatrix, if we knew that one was likely to be more risky to have the errors, 
we can use the t_matvec instead. However, for COOMatrix, we do not have that convenience.


* Which methods did you parallelize?  What directives did you use?  How much parallel speedup did you see for 1, 2, 4, and 8 threads?
I parallelized matvec and t_matvec methods of CSCMatrix and CSRMatrix. 
I used OpenMP critical directive for all 4 of them. 
The parallel speedup for 1, 2, 4, and 8 threads are not really significant, the performances do 
improve though. Compare to others, the parallel speedup from 4 threads to 8 threads was relatively the 
most significant, especially for CSR and CSC^T (increased by about 0.05 GFlops/s). Other speedups 
were generally less than 0.02 GFlops/s. 


Sparse Matrix Dense Matrix Product (AMATH583 Only)
--------------------------------------------------

* Which methods did you parallelize?  What directives did you use?  How much parallel speedup did you see for 1, 2, 4, and 8 threads?  How does the parallel speedup compare to sparse matrix by vector product?
I parallelized the matmat functions in CSRMatrix.hpp and CSCMatrix.hpp. 
I used OpenMP critical directives for both of them. 
With the small size of B matrix (like 1 col, 2 cols, and 3 cols), the speedup for 1, 2, 4, and 8 threads 
was not significant (most of them even did not improve at all). 
However, as the size of B matrix increased, the speedup for 1, 2, 4, and 8 threads was relatively 
significant. 
When the B matrix had 10 columns, the speedup was up to 1 GFlops/s. 
With the small size of B matrix (like 1 col, 2 cols, and 3 cols), the speedup was not really 
significant, which was like the sparse matrix by vector product. 
However, as the size of B matrix increased, the speedup was much more significant than the speedup 
for sparse matrix by vector product. 


PageRank Reprise
----------------

* Describe any changes you made to pagerank.cpp to get parallel speedup.  How much parallel speedup did you get for 1, 2, 4, and 8 threads?
I commented 'CSRMatrix A = read_csrmatrix(input_file);' 
and changed to 'CSCMatrix A = read_cscmatrix(input_file);'
Additionally, I add '#pragma omp critical' in front of the for loops under Call pagerank and 
Rank order the result. 
The parallel speedup I got for 1, 2, 4, and 8 threads was generally 0.05 GFlops/s. This improvement 
might seem small, however, since my original performances were small (all less than 1.5 GFlops/s, 
and more than one half of them were less than 1 GFlops/s), this improvement was fine.


* (EC) Which functions did you parallelize?  How much additional speedup did you achieve?
I parallelized the piscetize functions for COO Matrix, CSR Matrix, CSC Matrix, and AOS Matrix (which 
we did not include in this assignment) in amath583sparse.cpp. I add '#pragma omp critical' in front of 
the for loops inside the functions (they are commented now). 
The additional speedup was not significant; some of them were increased by 0.01 GFlops/s to 
0.03 GFLops/s, and some of them were almost same as before. 
I parallelized almost all the for loops in amath583.cpp as well (they are commented now).
And I got the similar result, but the improvement was a little bit higher. 


Load Balanced Partitioning with OpenMP (Extra Credit)
-----------------------------------------------------

* Are there any choices for scheduling that make an improvement in the parallel performance (most importantly, scalability) of pagerank?

To-Do 5:
a) The most important thing I learned from this assignment was... 
The most important thing I learned from this assignment was OpenMP, the relevant directives, 
the relevant implementations, and all the other things about it. 
I learned how strong and useful the OpenMP is. 
I had no idea about OpenMP before I take this course, and I am excited that I could have a chance 
to play around with it. 

b) One thing I am still not clear on is...
One thing I am still not clear on is that I am confused about the To-Do 4 and Question 11. 
I kind of did not know how to do To-Do 4, like where should I add '#pragma omp parallel for 
schedule (static)' on. Additionally, I do not think that I know many choices for scheduling 
that make an improvement in the parallel performance of pagerank. 
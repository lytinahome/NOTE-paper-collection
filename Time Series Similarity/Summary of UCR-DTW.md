
# Searching and Mining Trillions of Time Series Subsequence under Dynamic Time Warping

### The problem it wants to solve and the conclusion. 
- Solved the problem of **Search on sequential data**: Find the closest subsequence to the target in a massive data file (up to trillion), the distance can be defined by DTW (Dynamic Time Warping) or ED (Euclidean Distance).
- The paper proposed **UCR-DTW** method to solve the problem above. And...
  - It is a fast exact search method (not approximate search).
  - It is better than index-based methods and other state-out-of-art method. 
  - The same idea works for both streaming data, and batch offline search.
- DTW is still good for real-time problems and large scale problem.

### A scenario to implement this method.
- **gene problems**: (see section 5.3 in the paper) 
> Using a relative short piece of DNA (with the length of 0.7million) in Human as the target, and find out the genes piece (from a 2 billion long DNA query) in other five primates which is closest to the target respectively using the method proposed in this paper. The cluster those six DNA piece, it turns out the clustering relationship reflects the correct biological grouping info. 

### how this method works.
The main idea of this method is to pruning the sequences and filtering out the candidate in order to reduce the needed calculation. 

Below are some Existing tricks:
  + using squared distance 
> this one is intuitive.
  + using lowering bounding 

![LB pruning](pics/lb.PNG)
> use the lower bound to preprune the candidate subsequence. It is mainly for accelerating DTW. There are many types of lower bounds, the key idea of keogh LB is define a envelop of the target sequece. 
  + early abandoning of ED and LB_keogh

![Early abd pruning](pics/ed.PNG)
> abandon the candidate when it exceed the best-so-far LB in halfway.
  + early abandoning of DTW

![DTW pruning](pics/dtw.PNG)
> abandon the candidate when its DTW[1:k] + LB[k+1:] exceed the best-so-far.
 
Below are some Novel tricks -- called **UCR suite**:
  + early abandoning of Z-normalization

![Z-normalize pruning](pics/z-normalization.PNG)
> using a sliding window to normalize the data and calculate the distance. Be careful of the accumulated floating error, need to refresh every millions. 
  + reodering early abandoning

![reorder pruning](pics/ordering.PNG)
> optimize the ordering to accelerate the early abandoning process.
  + reversing the query/data role in LB_keogh
  
![reverse pruning](pics/eq-ec.PNG)
> envelope the candidate (LB_EC) instead of target (LB_EQ) when calculating the lower bound, when all other lower bounds fail to prune. in fact, the operation won't use overhead space and save time overall. Note that LB_EC doesn't equal to LB_EQ.
  + cascading lower bounds

![lbtype pruning](pics/diff-lb.PNG)
> there is a trade-off between time complexity and tightness of the lower bound. in pactice we will use all effective LB to prune the data.

### some take aways from the paper
+ similarity search is core subroutine in many data mining algorithm.
+ DTW is an ubiquitious distance metrics for sequential data. It outperforms in many dataset.
+ ED is a sepcial case of DTW.
+ Normalization is important in the similarity search. in this paper, we use the mean-variance normalization. Each subsequence needs to be normalized individually. 
+ Indexing can help in the search problem but index method has the limitation on large scale problem (the key is each subseq needs to normalized). 
+ other interesting topic in time series: motif dicovery, anomaly detection, clustering, and classification.
### code and related work.
+ [code](http://www.cs.ucr.edu/~eamonn/UCRsuite.html)
+ [DTW](https://dynamictimewarping.github.io/)
+ [An Overview of sequential data](https://github.com/lytinahome/Paper-collection/blob/master/Time%20Series%20Similarity/Overview_Experimental_comparison_time_series.pdf)
 
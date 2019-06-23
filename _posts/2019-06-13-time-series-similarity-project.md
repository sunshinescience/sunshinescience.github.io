---
layout: post
title: "Determining the difference between sampled data within similar time ranges"
---
<img src="/assets/img/diffing_curves.png" width="700" height="500">

There appears to be a recurring question on researchgate.net and stackexchange.com. This question has been observed in various forms: "How can I compare two curves?" Whether it be "How can I compare the shape of two curves" or "How to compare features of two curves" or "Similarity measures between two curves," there appears to be a number of similar questions regarding determining the similarity or dis-similarity of two (or more) curves. There are numerous applications for comparing whether or not curves are similar, for example, oil, finance time series, geochemistry, and things such as the projected density of states of atomic orbitals of a compound, and etc. These thoughts came to mind while doing the yield curve blog, as this could prove useful at identifying similar trends in yield curves. The original goal of this demonstration was to do the following: 
-  Find different ways to compare time series 
-  Quantify the similarity or dis-similarity of two (or more) time series
-  Develop a Python library that 1) quantitatively compares curves and 2) compares the shapes of curves

However, while beginning to work on the code, I stumbled across [this](https://pypi.org/project/similaritymeasures/) useful library, which had already been done. That's pretty much what was in mind for this project. So, instead, the work that had already been started will be shown below.

## Calculate the distance and area between two curves
Let's have a look at what was started for this project. The function `compare_curves` below analyzes the overlapping region (i.e., overlapping x-values) between two 2-D curves of sampled data. The function provides minimum, maximum, and mean distance measurements between the two curves within the overlapping range. It also calculates the area within that region. Statistics between the curves within that overlapping region are provided. And lastly, the function prints how similar the curves are to one another (based on the obtained r-value). 

    def compare_curves(xs1, ys1, xs2, ys2):
        """
        Parameters:
        xs1: input array of x values from sampled data 
        ys1: input array of y values of sampled data
        xs2: input a second array of x values of sampled data
        ys2: input a second array of y values of sampled data
        
        NOTE: the x1 and y1 values must have the same shape. And the
        x2 and y2 values must have the same shape.
        
        Finds the overlapping x-values and analyzes that region between two curves.
        Return the absolute minimum, maximum, and mean vertical distances between two curves.
        Return the absolute area between two curves.
        A plot of the curves is provided with the area shaded.
        """

        # Determine the overlapping x- values in order to align the values between the two curves to compare them
        xs1_min = min(xs1)
        xs1_max = max(xs1)
        xs2_min = min(xs2)
        xs2_max =  max(xs2)
        xs_overlap_min = max(xs1_min, xs2_min)
        xs_overlap_max = min(xs1_max, xs2_max)
        # print (f"Overlapping x: [{xs_overlap_min}, {xs_overlap_max}]") # The min and max overlap values, provides two overall values for the min and max overlap range between the two curves
        
        xs1_overlap_where = np.where((xs1 >= xs_overlap_min) & (xs1 <= xs_overlap_max)) # x values >= the min of both curves and x values <= the max of both curves
        xs1_overlap = xs1[xs1_overlap_where] # xs1 overlap values, these are the points along the xs1 curve within the overlap range
        
        # Interpolate master curve
        f2 = interpolate.interp1d(xs2, ys2, kind='cubic')
        ys2_interp_overlap = f2(xs1_overlap) # y values of the interpolated line at the points of xs1_overlap range
        
        # Compute vertical difference in y values between the y values of ys1 within the xs1 overlap range and the y values of the interpolated line (ys2) at the ys1 points of xs1 overlap range
        diff_overlap = abs(ys1[xs1_overlap_where] - ys2_interp_overlap) # y values of ys1 within the overlap range minus the y values of the interpolated line at the ys1 points of overlap range
        # print ('diff overlap: ', diff_overlap)
        
        # Compute Euclidean distance between the above two curves (same as above vertical difference calculation because the x-values here are the same now)
        Euc_dist = np.sqrt((xs1_overlap - xs1_overlap)**2 + (ys1[xs1_overlap_where] - ys2_interp_overlap)**2) 
        
        # Compute min, max, mean values between curves
        diff_overlap_min = min(diff_overlap)
        diff_overlap_max = max(diff_overlap)
        print ('Distance:','\nMinimum distance between curves: ', round(diff_overlap_min, 2))
        print ('Maximum distance between curves: ', round(diff_overlap_max, 2))
        print ('Mean distance between curves: ', round(np.mean(diff_overlap), 2))
        
        # Determine the index of both the min and max distances between two curves are within y_distance
        y_min = min(diff_overlap)
        y_max = max(diff_overlap)
        y_diff_overlap_index = [0]
        index = 0
        for i in diff_overlap:
            index = index + 1
            y_diff_overlap_index.append(index)
            if i == y_min:
                y_dist_index_min = index - 1 # Index of where the minimum distance between the curves is located within y_distance
            elif i == y_max:
                y_dist_index_max = index - 1 # Index of where the maximum distance between the curves is located within y_distance
        
        x_value_min = []
        x_value_max = []
        for i in range(2):    
            x_value_min.append(xs1_overlap[y_dist_index_min]) # x value where the minimum between the two curves is located, appending it twice in order to plot the min location between curves
            x_value_max.append(xs1_overlap[y_dist_index_max]) # x value where the maximum between the two curves is located, appending it twice in order to plot the max location between curves
        
        # Get endpoints at minimum and maximum distances between curves, to annotate plot
        diff_overlap_min_y = [] # Two y-axis end points where the minimum between the two curves is located
        diff_overlap_min_y.append(ys1[xs1_overlap_where][y_dist_index_min])
        diff_overlap_min_y.append(ys2_interp_overlap[y_dist_index_min])
        diff_overlap_min_y_middle = ((ys1[xs1_overlap_where][y_dist_index_min]) + (ys2_interp_overlap[y_dist_index_min]))/2 # Location of where to annotate an arrow along the y-axis
        diff_overlap_max_y = [] # Two y-axis end points where the maximum between the two curves is located
        diff_overlap_max_y.append(ys1[xs1_overlap_where][y_dist_index_max])
        diff_overlap_max_y.append(ys2_interp_overlap[y_dist_index_max])
        diff_overlap_max_y_middle = ((ys1[xs1_overlap_where][y_dist_index_max]) + (ys2_interp_overlap[y_dist_index_max]))/2 # Location of where to annotate an arrow along the y-axis
        
        # Compute the area in between the curves 
        curves_int1 = integrate.trapz(ys1[xs1_overlap_where], xs1_overlap)
        curves_int2 = integrate.trapz(ys2_interp_overlap, xs1_overlap)
        integrated_diff = abs(curves_int1 - curves_int2)
        print ('\nArea:', '\nArea between curves: ', round(integrated_diff, 2))
        
        # Calculate statistics between the curves, within the overlap range
        slope, intercept, r_value, p_value, std_err = stats.linregress(ys1[xs1_overlap_where], ys2_interp_overlap)
        print ('\nStatistics:', '\nr value:', '{:<0.3f}'.format(r_value), '\np-value:', '{:<0.3f}'.format(p_value))
        
        # Print the similarity of two curves, based on the obtained r value
        if r_value > 0.99:
            print ('\nThe curves are virtually identical.')
        elif r_value > 0.95:
            print ('\nThe curves have very high similarity.')
        elif r_value > 0.67 and r_value < 0.95:
            print ('\nThe curves have high similarity.')
        elif r_value > 0.33 and r_value < 0.67:
            print ('\nThe curves have some (moderate) similarity.')
        elif r_value > 0.05 and r_value < 0.33:
            print ('\nThe curves are not very similar.')
        elif r_value < 0.05:
            print ('\nThe curves do not have any similarity at all.')
        
        # Plot the curves
        plt.plot(xs1, ys1, 'bo', label='s1 data')
        plt.plot(xs1_overlap, ys2_interp_overlap, 'g-', label='Interpolated data')
        plt.plot(xs2, ys2, 'go', label='s2 data')
        plt.plot(x_value_min, diff_overlap_min_y, 'k--', linewidth=2, label='Min distance')
        plt.plot(x_value_max, diff_overlap_max_y, 'k-.', linewidth=2, label='Max distance')
        plt.legend(loc='best')
        plt.annotate('min distance', xy=((x_value_min[0]+.2), (diff_overlap_min_y_middle)), xytext=((x_value_min[0] + 1), (diff_overlap_min_y_middle+.1)),
                arrowprops=dict(arrowstyle="->", connectionstyle="arc3"))
        plt.annotate('max distance', xy=((x_value_max[0]-.2), (diff_overlap_max_y_middle)), xytext=((x_value_max[0] - 3), (diff_overlap_max_y_middle+.1)),
                arrowprops=dict(arrowstyle="->", connectionstyle="arc3"))
        plt.fill_between(xs1_overlap, (ys2_interp_overlap), ys1[xs1_overlap_where], color="crimson", alpha=0.2) # The area is shaded between the curves
        #plt.show()
        plt.savefig("straight_vs_curved.png", dpi=150) 

    xs1 = np.linspace(0, 10, 7)
    xs2 = np.linspace(2, 11, 9)
    ys1 = np.ones_like(xs1)
    ys2 = np.exp(-xs2/5.0)
    compare_curves(xs1, ys1, xs2, ys2)

The annotations on the plot from the above function illustrate the locations of the minimum and maximum distance between two curves:
<img src="/assets/img/straight_vs_curved.png" width="700" height="500">

The information generated from `compare_curves` is shown below. 

-  Distance: 

   Minimum distance between curves:  0.49
   Maximum distance between curves:  0.86
   Mean distance between curves:  0.71

-  Area: 

   Area between curves:  4.76

-  Statistics: 

   r-value: 0.000 
   p-value: 1.000

**The curves do not have any similarity at all.**
These curves above are not similar according to the statistics.

An example of alternate curves is shown below.

    xs1 = np.linspace(0, 10, 9)
    xs2 = np.linspace(2, 11, 9)
    ys1 = np.exp(-xs2/3.0)
    ys2 = np.exp(-xs2/5.0)
    compare_curves(xs1, ys1, xs2, ys2)

<img src="/assets/img/curved_vs_curved.png" width="700" height="500">

The information that the function provides is:
-  Distance: 

   Minimum distance between curves:  0.11
   Maximum distance between curves:  0.36
   Mean distance between curves:  0.22

-  Area: 

   Area between curves:  1.64

-  Statistics: 

   r-value: 0.995 
   p-value: 0.000

**The curves are virtually identical.**
The curves directly above show a high degree of similarity.

The full code from this demonstration can be found [here](https://github.com/sunshinescience/tsdiff/blob/873e23e96ac2d5350670751d2d6f563e2354225e/tsdiff.ipynb).

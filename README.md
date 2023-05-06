Download Link: https://assignmentchef.com/product/solved-ee2703-tutorial5-laplace-equation
<br>
We wish to solve for the currents in a resistor. The currents depend on the shape of the resistor and we also want to know which part of the resistor is likely to get hottest.

<h1>2            The Resistor Problem</h1>

A wire is soldered to the middle of a copper plate and its voltage is held at 1 Volt. One side of the plate is grounded, while the remaining are floating. The plate is 1 cm by 1 cm in size.

by the conductivity:

Now the Electric field is the gradient of the potential,

<h2>1   Volt</h2>

and continuity of charge yields

∇ <em>j </em>= −

<em>∂t</em>

Combining these equations we obtain As a result, current flows. The current at each

point can be described by a “current density” <em>~j</em>. This <em><sub>∂ρ </sub></em>current density is related to the local Electric Field <sup>∇</sup>·(−<em>σ</em>∇<em>φ</em>) = −

<em>∂t</em>

Assuming that our resistor contains a material of constant conductivity, the equation becomes

<sub>2            </sub>1 <em>∂ρ</em>

∇ <em>φ </em>=

<em>σ ∂t</em>

For DC currents, the right side is zero, and we obtain

2

∇ <em>φ </em>= 0

<h1>3            Numerical Solution in 2-Dimensions</h1>

Laplace’s equation is easily transformed into a difference equation. The equation can be written out in Cartesian coordinates

Assuming <em>φ </em>is available at points <em>x<sub>i</sub></em><em>,</em> we can write

<em>                 φ</em>(<em><sup>x</sup><sub>i</sub></em>+1<em>/</em>2<em>,y</em><em>j</em>)−<em>φ</em>(<em><sup>x</sup><sub>i</sub></em>−1<em>/</em>2<em>,y</em><em>j</em>)

<em>∂x</em><em>x</em>

and

<em>∂x</em>

Combining this with the corresponding equation for the <em>y </em>derivitives, we obtain

<em>φ</em><em>i</em><em>,</em><em>j </em>                                                 (1)

Thus, if the solution holds, the potential at any point should be the average of its neighbours. This is a very general result and the above calculation is just a special case of it.

So the solution process is obvious. Guess anything you like for the solution. At each point, replace the potential by the average of its neighbours. Keep iterating till the solution converges (i.e., the maximum change in elements of <em>φ </em>is less than some tolerance).

But what do we do at the boundaries? At boundaries where the electrode is present, just put the value of potential itself. At boundaries where there is no electrode, the current should be tangential because charge can’t leap out of the material into air. Since current is proportional to the Electric Field, what this means is the gradient of <em>φ </em>should be tangential. This is implemented by requiring that <em>φ </em>should not vary in the normal direction.

<h1>4            Code</h1>

<h2>Import the libraries</h2>

First import the libraries needed by python.

2 h<em>setup </em>2i≡ from pylab import * import mpl_toolkits.mplot3d.axes3d as p3

<h2>Define the Parameters</h2>

These are the parameters that control the run. They have the following defaults, which should be corrected by the user via commandline arguments (see sys.argv)

Nx=25; // size along x Ny=25; // size along y radius=8;// radius of central lead

Niter=1500; // number of iterations to perform

<h2>Allocate the potential array and initialize it</h2>

First initialize <em>φ </em>to zero. Note that the array should have Ny rows and Nx columns. (the x and y are flipped to make the solution fit the diagram). When you are done, <em>φ </em>should look like so:

<table width="240">

 <tbody>

  <tr>

   <td width="42"> 0 0  0    0  0  0  00</td>

   <td width="21">00000000</td>

   <td width="21">00000000</td>

   <td width="21">00000000</td>

   <td width="21">00000000</td>

   <td width="21">00000000</td>

   <td width="21">00000000</td>

   <td width="21">00000000</td>

   <td width="21">00000000</td>

   <td width="29">0 0 0 <sub></sub>0 <sub></sub>0 <sub></sub>0 <sub></sub>0 <sub></sub>0</td>

  </tr>

 </tbody>

</table>

Now use “where” to locate the points in the region with a radius of 8 of the centre. To do this, you need two arrays, one with x-coordinates and the other with y-coordinates with the same shape as the array. This is done with meshgrid:

Y,X=meshgrid(y,x)

where x and y are vectors containing the <em>x </em>and <em>y </em>positions. Note that you should set <em>x </em>= 0 and <em>y </em>= 0 in the middle of the region. Your condition to find the region that is 1 volt is then

<em>X </em>∗<em>X </em>+<em>Y </em>∗<em>Y </em>≤ 0<em>.</em>35∗0<em>.</em>35

Use where and capture the elements that satisfy this condition in an index array “ii”. Note that ii will have two columns, one giving the x-coordinate and the second the y-coordinate. Set the potential at these points to 1 by

phi[ii]=1.0

Plot a contour plot of the potential in Figure 1. Mark the <em>V </em>= 1 region by marking those nodes red.

<h2>Perform the iteration</h2>

During the iteration, we will use a second potential array, called oldphi to hold the values of the previous iteration. This is to keep track of how much the array changed during the current iteration. You cannot create it by assignment:

oldphi=phi

The problem is that a python array is a pointer. So the assignment above merely creates a new pointer to the existing data. Changing oldphi will change phi as well! That is not what we want. We want a new memory area. The way to do this with an assignment operation is to use the copy member function:

oldphi=phi.copy()

To keep track of errors, allocate an error vector, errors with Niter elements.

Iterate Niter times and record the errors. The structure of your for loop should be as follows:

for k in range(Niter)

save copy of phi update phi array assert boundaries errors[k]=(abs(phi-oldphi)).max();

#end

<h2>Updating the Potential</h2>

We first have to update the potential using Eq. 1. This is the central part of the algorithm. How can we do it? In C, it is just a nested pair of for loops:

for(i=1 ; i&lt;Ny-1 ; i++ ){ // interior nodes for(j=1 ; j&lt;Nx-1 ; j++ ){ // interior nodes phinew[i,j]=0.25*(phi[i,j-1]+phi[i,j+1]

+phi[i-1,j]+phi[i+1,j]);

}

}

This would result in <em>N<sub>x</sub>N<sub>y </sub></em>passes through the code and would reduce Python to a crawl. We have to do this same work in a single line of code.

<h3>Python subarrays</h3>

Note how python specifies sub arrays:

<ul>

 <li>To extract a sub array of <em>φ </em>say <em>φ</em>[2<em>,</em>3] to <em>φ</em>[5<em>,</em>8], we use</li>

</ul>

phi[2:4,5:9]

Note that the last element of our range is <em>not included in the subarray</em>.

<ul>

 <li>To extract the last ten rows and first eight columns of <em>φ </em>we use</li>

</ul>

phi[-10:,0:8]

Note that phi[-10:-1,0:8] would have got the last ten rows but would have left out the very last row.

With this information, we can now convert the for loops in C to vector operations in Python

<h3>Vectorizing For Loops</h3>

for(i=1 ; i&lt;Ny-1 ; i++ ){ // interior nodes for(j=1 ; j&lt;Nx-1 ; j++ ){ // interior nodes phinew[i,j]=0.25*(phi[i,j-1]+phi[i,j+1]

+phi[i-1,j]+phi[i+1,j]);

}

}

The way to “parallelize” this block of C code is to look at the slice of the matrix we work with. We are updating all the interior elements of phi that is phi[1:-1,1:-1].

Now consider something rather strange: The matrix of left neighbours of this slice, i.e., the matrix created by phi[i,j-1]. Clearly it can be described as the matrix phi[1:Ny-2,0:Nx-3], or equivalently, phi[1:-1,1:-2]. Note that the last row of the phi is Ny-1 and not Ny.

Proof: The (<em>i</em><em>, j</em>)<sup>th </sup>element of phi[1:Ny-1,1:Nx-1] is phi[i+1,j+1]. The (<em>i</em><em>, j</em>)<sup>th </sup>element of phi[1:Ny-1,0:Nx-2] is phi[i+1,j], which is the left neighbour of phi(i+1,j+1).

Similarly construct the matrix of right neighbours, top neighbours and bottom neighbours. Once this is done, the C code above can be written as a single line:

phi[1:-1,1:-1]=0.25*(phi[1:-1,0:-2]

+ phi[1:-1,2:]

+ top neighbours

+ bottom neighbours);

This is a matrix equation, which means that the for loops are still there, but they are executed in C within the Python built in functions. Code in this line in Python.

<h2>Boundary Conditions</h2>

At boundaries where the electrode is present, just put the value of electrode potential itself. At boundaries where there is no electrode, the gradient of <em>φ </em>should be tangential. This is implemented by requiring that <em>φ </em>should not vary in the normal direction.

The code to enforce the boundaries has to go in after the Poisson update. At the electrode, we do not have to do anything, since the edge row is not changed and the electrode potential is static. But at the other boundaries, we have to make the final row the same as the adjacent row.

For example, at the left boundary, we need to set the normal derivitive of potential to zero. This means that <em>∂φ</em><em>/∂x </em>= 0. This is achieved in C by

for( i=1 ; i&lt;Ny-1 ; i++ ) phi[i,0]=phi[i,1];

In Python, we do the same thing by the following vector command:

phi[1:-1,0]=phi[1:-1,1]

What does this line do? The <em>i</em><sup>th </sup>entry of this vector equation says

phi[i,0]=phi[i,1]

which is what we want. Similarly, for the right side we have

for( i=1 ; i&lt;Ny-1 ; i++ )

phi[i,Nx-1]=phi[i,Nx-2];

Convert it to Python code. Do the same for the top boundary.

The central portion is more difficult. During an iteration, the boundary values of the electrode can get over-written. So we have to recreate those values. But you have already captured the elements of the electrode in index vector ii, when setting up the potential. Then all you you have to do is:

phi[ii]=1.0

to restore that boundary condition. Use Vectorized code!

<h3>Graph the results</h3>

First show how the errors are evolving. They should reduce but very slowly. Use a semi-log plot. You will find that a log-log plot gives a reasonably straight line upto about 500 iterations, but beyond that, you get into the exponential regime. This is for a 30 by 30 grid.

To see individual data points, plot every 50<sup>th </sup>point.

Anytime we have something like an exponential, we should extract the exponent. However, note that it is an exponential decay only for larger iteration numbers. Let us try to fit the entire vector and only the portion beyond 500. Remember that if the fit is of the form

<em>y </em>= <em>Ae<sup>Bx</sup></em>

the thing to do is to take the log. Then we have

log<em>y </em>= log<em>A</em>+<em>Bx</em>

That is why it looks like a straight line in a semi-log plot.

Extract the above fit for the entire vector of errors and for those error entries after the 500<sup>th </sup>iteration. Plot the fit in both cases on the error plot itself. use legend to label the curves (errors, fit1, fit2).

<h2>Stopping Condition</h2>

So where should you stop? Or more precisely, what is the error? The maximum error scales as

error = <em>Ae<sup>Bk</sup></em>

where <em>k </em>is the iteration number. for our fit, For one choice of values, I got

error<em><sub>k </sub></em>= 0<em>.</em>0014exp(−0<em>.</em>00226<em>k</em>)

Summing up the terms, we have

∞

Error = ∑ error<em><sub>k</sub></em>

<em>k</em>=<em>N</em>+1

∞

<em>&lt; </em>∑ <em>Ae</em><em>Bk</em>

<em>k</em>=<em>N</em>+1

Z ∞

≈               <em>Ae<sup>Bk</sup>dk</em>

<em>N</em>+0<em>.</em>5

<em>A</em>

= −     exp(<em>B</em>(<em>N </em>+0<em>.</em>5))

<em>B</em>

Note that the inequality comes because the errors at any iteration could be either sign. So some of these errors <em>could </em>cancel. We look for a worst case result and sum up the absolute values. For our values, this expression evaluates to

Error <em>&lt; </em>0<em>.</em>63×0<em>.</em>03 = 0<em>.</em>02

Note that the last per-iteration change was 0<em>.</em>0000474, which shows you that that is extermely misleading. Take a look at the error plot in detail. The error went from 6×10<sup>−4 </sup>to 1<em>.</em>5×10<sup>−4 </sup>in 500 iterations. As you know, in exponential decay, what matters is the “half life” or the time constant. For this error vector, the time constant is 1<em>/B </em>= 442. That explains the difference, since 0<em>.</em>02<em>/</em>442 = 0<em>.</em>0000452. The profile was changing very little every iteration, but it was continuously changing. So the cumulative error was still large.

A general comment on the algorithm: This method of solving Laplace’s Equation is known to be one of the worst available. This is because of the very slow coefficient with which the error reduces.

<h2>Surface Plot of Potential</h2>

Next do a 3-D plot of the potential. For the default values I got the following plot (the boundary conditions were slightly different with <em>V</em><sub>2 </sub>= 0):

To create this plot, you need the following lines in your code:

fig1=figure(4)       # open a new figure ax=p3.Axes3D(fig4) # Axes3D is the means to do a surface plot title(’The 3-D surface plot of the potential’)

surf = ax.plot_surface(Y, X, phi.T, rstride=1, cstride=1, cmap=cm.jet,li

plot_surface does the real job of plotting. You can play with the options and see what they do. The current is primarily going from electrode to electrode, but it spreads in the middle.

<h2>Contour Plot of the Potential</h2>

Obtain a contour plot of the potential. For this look up the contour function. Again, mark the central electrode by red dots.

<h2>Vector Plot of Currents</h2>

Now obtain the currents. This requires computing the gradient. The actual value of <em>σ </em>does not matter to the shape of the current profile, so we set it to unity. Our equations are

<em>j<sub>x </sub></em>= −<em>∂φ</em><em>/∂x j<sub>y    </sub></em>= −<em>∂φ</em><em>/∂y</em>

This numerically translates to

Create the arrays Jx, Jy. Then call the quiver command: quiver(y,x,Jy[::-1,:],Jx[::-1,:])

What this does is to create the vector plot in the desired direction.

Plot the current density using quiver, and mark the electrode via red dots. Here is what I got:

The current fills the entire cross-section and then flows in the direction of the grounded electrode. Hardly any current flows out of the top part of the wire. Why not?

Most of the current is in the narrow region at the bottom. That is what will get strongly heated. If you feel motivated, take the currents calculated above, and now solve

where the right side represents heat generated from (ohmic loss). The boundary condition is <em>T </em>= 300 at the wire and the ground while <em>∂T</em><em>/∂n </em>= 0 at the other three edges. This will now show us where the heating is taking place (though it is obvious from the above plot), and how hot the plate will become.
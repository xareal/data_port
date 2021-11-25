
## 1. Patterns in nature
<p><img style="float: left;margin:2px 2px 2px 2px" src="https://assets.datacamp.com/production/project_62/img/phyllotaxis2.png" height="300" width="300">  </p>
<p><em>"The scientist does not study nature because it is useful; he studies it because he delights in it, and he delights in it because it is beautiful." (Henri Poincaré)</em></p>
<p>There are many examples of <em>natural facts</em> that can be described in mathematical terms. Nice examples are the shape of snowflakes, the <em>fractal geometry</em> of romanesco broccoli or how self-similarity rules the growth of plants.</p>
<p>R is a tool for doing serious analysis, but not everything in life is serious. Life is also funny, and R can be used to have fun and to do beautiful things. Its graphical power can be used to produce artistic images like the one that illustrates this section, which is inspired by how plants arrange their leaves. This fact is called <em>phyllotaxis</em> and will serve as the basis of this project.</p>
<p>In this notebook, we are using the <code>ggplot2</code> package. Apart from having fun, we will learn many important features of it that will be useful not only to do art but also to represent data in real-life problems. Let's start by loading the library.</p>


```R
# Set plot images to a nice size
options(repr.plot.width = 4, repr.plot.height = 4)

# Load the ggplot2 package
library(ggplot2)
```


```R
library(testthat) 
library(IRkernel.testthat)

run_tests({
    test_that("Test that ggplot2 is loaded", {
    expect_true( "package:ggplot2" %in% search(), 
        info = "The ggplot2 package should be loaded using library().")
    })
})
```






    1/1 tests passed


## 2. Warming up: drawing points on a circle
<p>There are many ways to represent data with <code>ggplot2</code>: from simple scatter plots to more complex violin plots. The functions that start with <code>geom_</code> define the type of plot. In this notebook, we will only work with <code>geom_point()</code> which plots points in two dimensions. We'll need a dataset with two variables; let's call them <code>x</code> and <code>y</code>.</p>
<p>We'll start by drawing 50 points on a circle of radius 1. As every <code>(x, y)</code> point should be in the unit circle, it follows that x² + y² = 1. We can get this using the <em>super famous</em> Pythagorean trigonometric identity which states that sin²(θ) + cos²(θ) = 1 for any real number θ.</p>


```R
# Create circle data to plot
t <- seq(0, 2*pi, length.out = 50)
x <- sin(t)
y <- cos(t)
df <- data.frame(t, x, y)

# Make a scatter plot of points in a circle
p <- ggplot(df, aes(x, y)) + geom_point()
p
```


![png](output_4_0.png)



```R
run_tests({
    test_that("Check that a geom_point plot was plotted.", {
    expect_true( "GeomPoint" %in% class( last_plot()$layers[[1]]$geom ) , 
        info = "Add geom_point() to produce a scatter plot.")
    })
})
```






    1/1 tests passed


## 3. Make it harmonious with the Golden Angle
<p>Plants arrange their leaves in spirals. A spiral is a curve which starts from the origin and <em>moves away</em> from the origin as it revolves around it. In the plot above all our points are the same distance from the origin. A simple way to arrange them in a spiral is to multiply <code>x</code> and <code>y</code> by a factor which increases for each point. We <em>could</em> use <code>t</code> as that factor, as it meets these conditions, but we will do something more <em>harmonious</em>. We will use the <a href="https://en.wikipedia.org/wiki/Golden_angle">Golden Angle</a>:</p>
<p style="text-align: center;">Golden Angle = π(3 − √5)</p>
<p>This number is inspired by the Golden Ratio, one of the most famous numbers in the history of mathematics. Imagine that you have a circumference and you break up it into two arcs with lengths <code>a</code> and <code>b</code>, with <code>a&gt;b</code> (an arc is a portion of the circumference). The angle that <em>breaks</em> the circle so that <code>a/b=(a+b)/a</code> is called the Golden Angle. In other words: the Golden Angle <em>breaks up</em> a circle so that the ratio of the big arc to the little arc is the Golden Ratio. This image (from Wikipedia) illustrates the previous definition:</p>
<p><img src="https://assets.datacamp.com/production/project_62/img/Golden_Angle.png" height="300" width="300"></p>
<p>The Golden Angle is the angle subtended by the smaller (red) arc. Both the Golden Ratio and the Golden Angle appear in unexpected places in nature. Apart of flower petals and plant leaves, you'll find them in seed heads, pine cones, sunflower seeds, shells, spiral galaxies, hurricanes, etc.</p>
<p>It's time to <em>spiralize</em>!</p>


```R
# Define the number of points
points <- 500

# Define the Golden Angle
angle <- pi * (3 - sqrt(5))

t <- (1:points) * angle
x <- sin(t)
y <-cos(t)
df <- data.frame(t, x, y)

# Make a scatter plot of points in a spiral
p <- ggplot(df, aes(x*t, y*t)) + geom_point()
p
```


![png](output_7_0.png)



```R
run_tests({
    test_that("points are 500.", {
    expect_equal(points, 500, 
        info = "There should be 500 points.")
    })
    
    test_that("angle is golden.", {
    expect_equal(angle, pi*(3-sqrt(5)), 
        info = "angle should be set to the Golden Angel. Check the hint!")
    })
})
```






    2/2 tests passed


## 4. Remove everything unnecessary
<p>Apart from data, a plot includes many other components that define its final appearance. Our previous plot contains:</p>
<ul>
<li>a <strong>background</strong> filled with grey color</li>
<li>a <strong>grid</strong> of horizontal and vertical white lines</li>
<li><strong>ticks</strong> along the axis</li>
<li>a <strong>title</strong> on each axis</li>
<li><strong>text</strong> along axes to label marks</li>
</ul>
<p>Art does not get along with most of these elements, so it's time to move to action.</p>


```R
df <- data.frame(t, x, y)

# Make a scatter plot of points in a spiral and remove some plot components
p <- ggplot(df, aes(x*t, y*t))
p + geom_point() +
# .... YOUR CODE FOR TASK 4 ....
theme(panel.grid= element_blank(), 
      axis.ticks= element_blank(),
      axis.title= element_blank(),
      axis.text= element_blank(),
      panel.background= element_rect(fill= "white"))
```


![png](output_10_0.png)



```R
run_tests({
    test_that("Background is white.", {
        expect_equal(last_plot()$theme$panel.background$fill, "white", 
                     info = "The background should be white.")
    })
    test_that("Plot components are removed.", {
        expect_true("element_blank" %in% class(last_plot()$theme$panel.grid), 
                    info = "The grid lines should be removed.")
        expect_true("element_blank" %in% class(last_plot()$theme$axis.ticks), 
                    info = "The axis ticks should be removed.")
        expect_true("element_blank" %in% class(last_plot()$theme$axis.title), 
                    info = "The axis titles should be removed.")
        expect_true("element_blank" %in% class(last_plot()$theme$axis.text), 
                    info = "The axis text should be removed.")        
    })
})
```






    2/2 tests passed


## 5. A bit of makeup: size, color and transparency
<p>Our drawing is starting to look like a plant, but we can better. By changing color, transparency (also called <em>alpha</em>), and size of the points, the image will become more appealing.</p>


```R
# Change the code from Task 4 to modify the 
# size, transparency, and color of the points
p <- ggplot(df, aes(x*t, y*t))
p + geom_point(size= 8, alpha= 0.5, color= "darkgreen") +
theme(panel.grid= element_blank(), 
      axis.ticks= element_blank(),
      axis.title= element_blank(),
      axis.text= element_blank(),
      panel.background= element_rect(fill= "white"))
```


![png](output_13_0.png)



```R
run_tests({
    test_that("Point size equal to 8.", {
    expect_equal(last_plot()$layers[[1]]$aes_params$size, 8, 
        info = "size should be set 8.")
    })
    test_that("alpha equal to 0.5.", {
    expect_equal(last_plot()$layers[[1]]$aes_params$alpha, 0.5, 
        info = "alpha should be set 0.5.")
    })
})
```






    2/2 tests passed


## 6. Play with aesthetics: the dandelion
<p>Until now, all points have the same appearance (<code>size</code>, <code>color</code>, <code>shape</code>, and <code>alpha</code>). Sometimes we will want to make the appearance of the points dependent on a variable in the dataset. Now we will make the size variable. We will also change the shape of the points. Although we won't be able to blow on it, the resulting image should remind you of a dandelion.</p>


```R
# Copy the code from Task 5 and modify the 
# color, size, and shape of the points
p <- ggplot(df, aes(size= t, x*t, y*t))
p + 
geom_point(aes(size= t), alpha= 0.5, shape= 8) +
theme(panel.grid= element_blank(), 
      axis.ticks= element_blank(),
      axis.title= element_blank(),
      axis.text= element_blank(),
      legend.position= "none",
      panel.background= element_rect(fill= "white"))
```


![png](output_16_0.png)



```R
run_tests({
    test_that("Map size of points to t.", {
    expect_equal(last_plot()$labels$size, "t", 
        info = "Map size of points to t. Check the hint!")
    })
    test_that("point shape is asterisk.", {
    expect_equal(last_plot()$layers[[1]]$aes_params$shape, 8, 
        info = "Change the shape of all points to asterisks.")
    })
    test_that("Legend is removed.", {
    expect_equal(last_plot()$theme$legend.position, "none", 
        info = "Remove the legend from the plot.")
    })
})
```






    3/3 tests passed


## 7. Put all it together: the sunflower
<p>Plants not only use the Golden Angle to arrange leaves. The Golden Angle is also found in the arrangement of sunflower seeds. We don't need anything new to draw a sunflower; we just need to combine some of the things we already know.</p>


```R
# Copy the code from Task 6 and modify the color and
# shape of the points, and the background color
p <- ggplot(df, aes(x*t, y*t))
p + 
geom_point(shape= 17, color= "yellow", alpha= 0.5) +
theme(panel.grid= element_blank(), 
      axis.ticks= element_blank(),
      axis.title= element_blank(),
      axis.text= element_blank(),
      legend.position= "none",
      panel.background= element_rect(fill= "darkmagenta"))
```


![png](output_19_0.png)



```R
run_tests({
    test_that("point shape is filled triangles.", {
        expect_equal(last_plot()$layers[[1]]$aes_params$shape, 17, 
                     info = "Change the shape of all points to filled triangles. Check the hint.")
    })
    test_that("The triangles are yellow", {
        expect_equal(last_plot()$layers[[1]]$aes_params$colour, "yellow", 
                     info = "The triangles are not yellow. Check the hint.")
    })
    test_that("The background is dark magenta", {
        expect_equal(last_plot()$theme$panel.background$fill, "darkmagenta", 
         info = "The background is not dark magenta. Check the hint.")
    })
})
```






    3/3 tests passed


## 8. What if you modify the angle?
<p>These patterns are very sensitive to the angle between the points that form the spiral. Small changes to the angle can generate very different images. Let's look at an example of that.</p>


```R
# Change the value of the angle
angle <- 2.0
points <- 1000

t <- (1:points)*angle
x <- sin(t)
y <- cos(t)
df <- data.frame(t, x, y)

# Copy the plotting code from Task 7
p <- ggplot(df, aes(x*t, y*t))
p + 
geom_point(shape= 17, color= "yellow", alpha= 0.5) +
theme(panel.grid.major= element_blank(), panel.grid.minor= element_blank(), 
      axis.ticks.x= element_blank(), axis.ticks.y= element_blank(),
      axis.title.x= element_blank(), axis.title.y= element_blank(),
      axis.text.x= element_blank(), axis.text.y= element_blank(),
      legend.position= "none",
      panel.background= element_rect(fill= "darkmagenta"))
```


![png](output_22_0.png)



```R
run_tests({
    test_that("angle is 2.", {
    expect_equal(angle, 2, 
        info = "angle should be equal to 2")
    })
})
```






    1/1 tests passed


## 9. All together now: imaginary flowers
<p><img style="float: left;margin:2px 2px 2px 2px" src="https://assets.datacamp.com/production/project_62/img/flower.png" height="280" width="280"></p>
<p>The techniques we've used so far allow us to create an <em>infinite</em> number of patterns inspired by nature: the only limit is our imaginations. But making art has also been a fun excuse to learn to use <code>ggplot2</code>. All the tricks we have seen in this notebook are useful when plotting <em>real</em> data too.</p>
<p>The image on the left is a simple variation of the previous flower and is in essence very similar to the first figure in which we plotted 50 points in a circle. I hope you've enjoyed the journey between that simple circle and this beautiful flower.</p>


```R
# Change the values of angle and points
angle <- 13 * pi / 180
points <- 2000

t <- (1:points)*angle
x <- sin(t)
y <- cos(t)
df <- data.frame(t, x, y)

# Adjust the plot parameters to create the magenta flower
p <- ggplot(df, aes(x*t, y*t))
p + geom_point(size = 80, alpha = 0.1, shape = 1, color = "magenta4")+
  theme(legend.position="none",
        panel.background = element_rect(fill = "white"),
        panel.grid=element_blank(),
        axis.ticks=element_blank(),
        axis.title=element_blank(),
        axis.text=element_blank())
```


![png](output_25_0.png)



```R
run_tests({
    test_that("points is equal to 2000.", {
    expect_equal(points, 2000, 
        info = "There should be 2000 points.")
    })
    test_that("point shape is empty circle.", {
    expect_equal(last_plot()$layers[[1]]$aes_params$shape, 1, 
        info = "Change the shape of all points to empty circles. Check the hint!")
    })
    test_that("alpha is equal 0.1", {
    expect_equal(last_plot()$layers[[1]]$aes_params$alpha, 0.1, 
        info = "alpha of points should be 0.1")
    })
    test_that("Background is white.", {
    expect_equal(last_plot()$theme$panel.background$fill, "white", 
        info = "The background should be white.")
    })
    test_that("angle is 13*pi/180.", {
    expect_equal(angle, 13*pi/180, 
        info = "angle should be set to 13*pi/180.")
    })
})
```






    5/5 tests passed


In the assignment 2 we implement a diagram for comparing anual gdp of US states
from 1997 to 2020.
The first step in this task is to load the csv data, then I haved prepared the data
by transforming it into a new format as an array that has the name of the state and 
an array containing gdp of the said state per each year, called annualGDP.
Then we start to plot the diagram by creating each axis and the scales, which is linear
on the Y axis and Time on X axis.
Next step we prepare where the brush must be placed, but the actual function is implemented
at the end of the code.
Next I drew the lines based on the data first on the actual diagram then on the brush area.
Then I added the labels of each Axis.
At last interactions functions are defined for both on mouse over and on click to show the state
name and highlight the lines which have been clicked.
Next function which got implemented was brush funtionality, which led to the lines clipping
the diagram, which I prevented by clip paths.

additional to the course material and d3tutorial I have used the following sources for the 
mentioned reasons:

1. To correct year format on the X-Axis:
https://stackoverflow.com/questions/39649525/how-do-i-generate-axis-tick-for-every-year-in-d3

2. To find max of 2d array:
https://stackoverflow.com/questions/31249419/how-to-find-max-value-from-a-2d-matrix-using-d3-js

3. To change tickformat on the Y-Axis to millions:
https://stackoverflow.com/questions/39981780/d3-js-format-axis-uniformly-in-millions

4. To pass parameter to on.mouseover:
https://stackoverflow.com/questions/10000083/javascript-event-handler-with-parameters

5. To highlight the line on click:
https://newbedev.com/d3-js-change-color-and-size-on-line-graph-dot-on-mouseover

6. To prevent the lines from clipping the diagram:
https://bl.ocks.org/35degrees/02e5fd2eade71c0a6ffc01d5282def23
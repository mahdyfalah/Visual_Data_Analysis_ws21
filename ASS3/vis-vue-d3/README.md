Mohammad Mahdi Fallah, 01428941
In Assignment 3 we must implement a interactive scatter plot and a choropleth map of the usa.
I started the implementation by cloning the boilerplate code from https://github.com/asilcetin/vis-vue-d3
And I implemented the scatterplot inside scplot.vue (for some reason I had to change the name of the file since my
vscode was indicating an error with the file name but I couldn't fix it).
For the scatterplot I started by drawing the axises in scale of minimum and maximum of each data set, then I imeplemented a method to draw the color pallete(I chose this pallete since it had red and blue color which are two contrasting colors and I saw us election results be shown with these colors, so I thought this would be best fitting on a us map).
At last I implemented a method for plotting the circles which are our datapoints with respect to scale, and later I added some interactivability to this method to show name of each datapoint.
To make the year slider work inside whatch I implemented functions to look for changes in data and redraw the plot in case of changes.
for choropleth map I implemented drawMap method which is called in mounted to work at the start(same as scatterplot).
In drawMap i used the us-states-geo.json file to plot a svg of different states of the us, and gave them IDs to later be selected and change their color with respect to the scatterplot color pallet.
the other method that got implemented here was color(), in order to compute the correct color for each datapoint.
At last for interactivity I added both data sets to whatch to look for changes in data and redraw states with the correct color.
I also implemented a brush in scatterplot to store datapoints that are selected and only show color of those datapoints on the map.
this was possible by storing the points IDs on state.selectedStates in store.js and adding a watch for selectedStates in choropleth.vue so that the color of each path is chosen based on the selected states

External sources used:
D3:
    Boilerplate code:
        https://github.com/asilcetin/vis-vue-d3

    scatterplot and choropleth map:
        https://bl.ocks.org/cmgiven/abca90f6ba5f0a14c54d1eb952f8949c

    joining datasets:
        https://stackoverflow.com/questions/17817849/d3-js-how-to-join-data-from-more-sources/37083707

    color pallete:
        https://slu-opengis.github.io/biscale/reference/bi_pal.html

    assigning id to different elements:
        https://stackoverflow.com/questions/31381129/assign-new-id-attribute-to-each-element-created

    selecting by id:
        https://stackoverflow.com/questions/22360355/how-to-select-a-d3-svg-path-with-a-particular-id

Javascript:
    checking if array includes an element:
        https://www.w3schools.com/jsref/jsref_includes_array.asp

    removing space from string:
        https://stackoverflow.com/questions/5963182/how-to-remove-spaces-from-a-string-using-javascript

css:
    css colors:
        https://www.w3schools.com/cssref/css_colors.asp
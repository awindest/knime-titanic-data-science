<script>
    import { onMount } from 'svelte'
</script>

<h1> Data Science with Knime </h1>
<p></p>
<section>
    <h2 class="toc">0 - Introduction</h2>
    <p>You are reading a short article explaining an introductory data science workflow using Knime. In this workflow
        we are using a Random Forest Model to determine survivorship with Titanic passenger data. 
        Here is the Knime about screen. Note the version number. If you are using a different version of Knime
         then your screens may not exactly match what is shown here.
    </p>
<figure>
    <img class="dialog" src='/imgs/i0.webp' alt='Knime workflow' usemap="#heromap" />
</figure>    
<p>The data and the Knime workflow are part of the repository 
        [<a href="https:/github.com/awindest/knime-titanic-data-science">found here</a>]so you won't have to search for it on Kaggle.
        The workflow is shown below with each of the nodes in the workflow labeled with a number corresponding 
        to the numbered sections below.
        The Titanic problem is a classification problem. Classify passengers as survivors or, well... 
        This simplifies our selection of a modeling algorithm. Here we will use a random forest because it works
        well for problems of this nature and it is fairly robust to overfitting. Now we could use Support Vector
        Machines, KNN, K-Means, Naive Bayes or some boosted algorithms but here we believe Random Forest will work 
        quite well. And since our data isn't too voluminous we are not worried about one of the weaknesses of this model, performance.
    </p>

    <img class="hero" src='/imgs/header.png' width="1024px" alt='Knime workflow' usemap="#heromap" />

    <p>Enjoy!</p>
</section>

<section id='section1'>
    <h2>1 - CSV Reader</h2>
    <p>This Knime component reads in the passenger data from the file: Titanic-Dataset.csv which was obtained from Kaggle.
    No transformations were performed.</p>
    <img class='dialog' src='/imgs/i1.webp'  alt='Read CSV file' />  
<p></p>
</section>

<section id='section2'>
    <h2>2 - Statistics View</h2>
    <p> We always profile the data and see things like missing data which we will fix as well as identify what attributes are important for the model and which ones probably won't help.
        Here we see a statistical analysis of the Titanic dataset using the Statistics Knime node. The number of missing age values is highlighted.</p>
    <img class='dialog' src='/imgs/i2.webp'  alt='Statistical analysis' />  
    <p> 
        Most of the attributes look okay except we have 177 missing values for the age field. We will fix that a little later on by simply replacing the missing values with the average age.
        Some of you may have a philosophical difference of opinion and believe that is too simple or we should not include the missing values in our analysis. Here I'm just showing one way to do the analysis.
    </p>

</section>
<section id='section3'>
    <h2>3 - Missing Value</h2>
    <p> Let's fix the missing age values by replacing them with the mean age. To do this we use the Missing Value node, configure it for the Age field 
        and set it up as seen below in this oversized image. 8)</p>
    <img class='dialog' src='/imgs/i3.webp'  alt='Missing Value' />  
    <p></p>

</section>
<section id='section4'>
    <h2>4 - String Manipulation</h2>
    <p>Here we begin manipulating the strings to further classify our data, in this case, we are parsing the title which are 
    the first four characters after the first comma of the Name field. The title values are 'Mr. ','Dr. ', 'Mrs.', 'Miss' and 'Mast'. We see that 
    The Rule Engine node while powerful takes some getting used to. The $Name$ is how you select the variable. The indexOfChars returns the 
    index of the string, then we move over two characters and then copy the next four characters out of the $Name$ field. 
    After running this node, the field, First4Title has the title values.</p>
     <img class='dialog' src='/imgs/i4.webp'  alt='String Manipulation' />  
    <p></p>
</section>
<section id='section5'>
    <h2>5 - Rule Engine</h2>
    <p>In this Rule Engine node we are classifying each person as an adult or a child using the variable $First4Title$ we created 
    in the previous step:
    ("Mrs.", "Mr. ", "Dr. ") =&lt "Adult" and ("Miss", "Mast") =&lt "Child" </p>I
    <img class='dialog' src='/imgs/i5.webp'  alt='Rule Engine' />  
    <p></p>

</section>
<section id='section6'>
    <h2>6 - Rule Engine</h2>
    <p>    In this Rule Engine node we are classifying each person into a new variable, $AgeBracket$, 
        based upon their age using the variable $Age$:</p>
     <div style="display: flex; justify-content: center;">
     <pre>
    $Age$ &lt 3 => "Infant"
    $Age$ &lt 12 => "Child"
    $Age$ &lt 20 => "Teenager"
    $Age$ &lt 55 => "Adult"
    $Age$ &lt 90 => "Senior"
    </pre>
</div>
    <img class='dialog' src='/imgs/i6.webp'  alt='Rule Engine' />  
    <p></p>

</section>
<section id='section7'>
    <h2>7 - Rule Engine</h2>
<p>    The reason for the preceeding two nodes was to create $AgeBracket$ and $AgeBracket2$ columns and fill in any missing values of $AgeBracket$ 
    with $AgeBracket2$. We accomplish this with the Rule Engine node and the following code:</p>
    <div style="display: flex; justify-content: center;">
    <pre>
        MISSING $AgeBracket$ =&gt $AgeBracket2$
        TRUE =&gt $AgeBracket$
    </pre>
    </div>
    <p>which copies the value of $AgeBracket2$ into $AgeBracket$ if $AgeBracket$ has no value.</p>
    <img class='dialog' src='/imgs/i7.webp'  alt='Rule Engine' />  
    <p></p>

</section>
<section id='section8'>
    <h2 class="toc">8 - Number to String </h2>
<p>    Now we need to classify our Number variables to String variables as this is important for our model to work well.
    It needs categorical or ordinal or classifier variables so we use the Number to String node:</p>
    <img class='dialog' src='/imgs/i8.webp'  alt='Number to String Node' />  

    <p></p>

</section>
<section id='section9'>
    <h2>9 - Domain Calculator</h2>
    <p>The Domain Calculator scans the data and updates the possible values list and/or the min- and max values of selected columns. This node is useful when the domain 
        information of the data has changed and must be updated in the table specification, 
        for instance, the domain information as contained in a table spec may be void when a row filtering (e.g. outlier removal)
        is carried out so it is necessary to run this node to "reset".</p>
    <img class='dialog' src='/imgs/i9.webp'  alt='Domain Calculator' />  
    <p></p>

</section>
<section id='section10'>
    <h2>10 - Partitioning</h2>
    <p>We partition the dataset 80/20 using the Partitioning node. This means 80% of our data will train the model and the other 20% will be our testing data.</p>
    <img class='dialog' src='/imgs/i10.webp'  alt='Partitioning' />  
    <p></p>

</section>
<section id='section11'>
    <h2>11 - Random Forest Learner</h2>
    <p> A decision tree model makes a decision based upon branches from numerous input variables and use these
        to produce groupings of outcomes. Each result or outcome will fit into one of those groupings. When the model
        entertains a record it has not already sorted, such as one we are asking it to make a prediction for, 
        it finds which grouping it fits into best by going through the decision tree criteria, and where it ends up
        is the prediction. Cool, huh?
    </p>
    <img class='dialog' src='/imgs/i11.webp'  alt='Random Forest Learner' />  
    <p></p>

</section>
<section id='section12'>
    <h2>12 - Random Forest Predictor</h2>
    <p> The Random Forest Predictor node predicts patterns according to an aggregation of the predictions of the individual 
        trees in a random forest model. It takes the model we created in the Random Forest Learner node and applies it to the test data.</p>
    <img class='dialog' src='/imgs/i12.webp'  alt='Random Forest Predictor' />  
    <p></p>

</section>
<section id='section13'>
    <h2>13 - Scorer</h2>
    <p>How do we know actually how we did? We use the Scorer node. The Scorer node compares two columns, in this case, Survived and Prediction (Survived), by their attribute value pairs and shows the confusion matrix, i.e. how many rows of 
        which attribute and their classification match. The dialog allows you to select two columns for comparison; the values 
        from the first selected column are represented in the confusion matrix's rows and the values from the second 
        column by the confusion matrix's columns. The output of the node is the confusion matrix with the number of 
        matches in each cell. Additionally, the second out-port reports a number of accuracy statistics such as 
        True-Positives, False-Positives, True-Negatives, 
        False-Negatives, Recall, Precision, Sensitivity, Specificity, F-measure, as well as the overall accuracy and Cohen's kappa .</p>
    <img class='dialog' src='/imgs/i13.webp'  alt='Scorer' />  
    <p></p>

</section>

<p>We hope you have enjoyed this quick tour of one of the ways of predicting survivorship of the Titanic passengers.</p>


<style>
img { /* all images have a natural, realistic drop shadow */
    box-shadow: var(--shadow-elevation-medium);
}

.hero, .dialog {
    display: block;
    margin-left: auto;
    margin-right: auto;
    width: 85%;
    /* padding: 1em; */

}
.dialog {
    width: 75%;
    inset: 2em;
    border-radius: 1.5em;
    /* padding-block: var(--size-7) var(--size-7); new CSS - padding left and right */

}

:root {
    /* https://www.joshwcomeau.com/css/designing-shadows/ */
    --shadow-color: 211deg 11% 62%;
	--shadow-elevation-low:
		0.3px 0.5px 0.7px hsl(var(--shadow-color) / 0.34),
		0.4px 0.8px 1px -1.2px hsl(var(--shadow-color) / 0.34),
		1px 2px 2.5px -2.5px hsl(var(--shadow-color) / 0.34);
	--shadow-elevation-medium:
		0.3px 0.5px 0.7px hsl(var(--shadow-color) / 0.36),
		0.8px 1.6px 2px -0.8px hsl(var(--shadow-color) / 0.36),
		2.1px 4.1px 5.2px -1.7px hsl(var(--shadow-color) / 0.36),
		5px 10px 12.6px -2.5px hsl(var(--shadow-color) / 0.36);
	--shadow-elevation-high:
		0.3px 0.5px 0.7px hsl(var(--shadow-color) / 0.34),
		1.5px 2.9px 3.7px -0.4px hsl(var(--shadow-color) / 0.34),
		2.7px 5.4px 6.8px -0.7px hsl(var(--shadow-color) / 0.34),
		4.5px 8.9px 11.2px -1.1px hsl(var(--shadow-color) / 0.34),
		7.1px 14.3px 18px -1.4px hsl(var(--shadow-color) / 0.34),
		11.2px 22.3px 28.1px -1.8px hsl(var(--shadow-color) / 0.34),
		17px 33.9px 42.7px -2.1px hsl(var(--shadow-color) / 0.34),
		25px 50px 62.9px -2.5px hsl(var(--shadow-color) / 0.34);
}
@media (prefers-reduced-motion: no-preference) {
    :has(:target) {
        scroll-behavior: smooth;
        scroll-padding-top: 3rem;
    }
}
p {
    padding-block: var(--size-7); /* new CSS - padding left and right */
    margin-block: var(--size-7); /* new CSS - margin top and bottom */
    margin-left: var(--size-12);
	max-inline-size: 100ch;
}
</style>
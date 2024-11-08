# ASSIGNMENT: Sampling and Reproducibility in Python

Read the blog post [Contact tracing can give a biased sample of COVID-19 cases](https://andrewwhitby.com/2020/11/24/contact-tracing-biased/) by Andrew Whitby to understand the context and motivation behind the simulation model we will be examining.

Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

Modify the number of repetitions in the simulation to 1000 (from the original 50000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

# Author: Nusrat Khan

```
Sampling occurs in three stages in this script, infection sampling, primary contact tracing and secondary contact tracing. The blog post points out how contact tracing gives a biased view of the actual nature of disease spread, where we under/over estimate the importance of a particular place/event. This model simulate_event(m) is used to demonstrate that effect. The model produces a different output each time, sometimes overestimating the importance of weddings in contact tracing and sometimes underestimating it. 
The script is built as follows: 
To build the simulation model defined "m" Whitby starts with setting the parameters. Attack rate 10%, trace success 20% and secondary trace threshold is 2. In the next step he used pandas to create the data frame. An event list is created 1000 events where there are 200 weddings and 800 brunches. The data frame ppl is created, where the event column shows the type of event, infected is set to False which means no one starts off with being infected, and traced is NaN so they have not been traced yet.
The traced column is set to a boolean nullable type, so it can have three possible values as opposed to the standard boolean two. Here the column can have True, False or Nan for missing values. If someone is not traced their status will remain Nan. 

The next step is to randomly infect 10% of the people. A variable is created, infected_indices, the np.random.choice ()function is used to randomly select 10% of the people from the 1000, with no replacement. So a person an only be picked once. The next part ppl.loc() is a loc indexer which will allow the subset of infected people to be accessed. In the 'infected' column the status of infection will be stored, the =True part means the infection status will be updated to true is the person is infected. 

Then in primary tracing the infected individuals are identified.  Here whitby chooses to randomy decide which infected people get traced. The  ppl.loc[ppl['infected'], 'traced'] part selects the rows where infected is true, then it accesses the traced column where the tracing status will be updated. Here np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS, random numbers are generated between [0,1] where each infected person is assigned a random number where the value represents a random chance for each person to be infected. The result of this expression will compare the random value to the success rate 20% and see if the person has been traced or not, true if traced false if not. 


The next part implements secondary contact tracing based on event attendance. Here contacts of infected individuals from each event are further traced if 2 infected people are found in that particular event from primary tracing. 
event_trace_counts filters the people who have been successfully traced and creates a subset in the dataframe of only traced people. From this subset the event column is selected which indicates which even the person attended. Then the value_counts function counts the number of times each event appears in the subset of traced people. The secondary trace threshold condition specifies the minimum number of traced individuals required to do further tracing in that event. ppl.loc traces the infected individuals who attended an event we are going to do further tracing on. 

Then the proportions of infections and traced infections associated with different event types is caculated. A new event type column is made with 'w' and 'b' for the events. wedding_infections sums up the number of people who were infected and attended a wedding. And brunch_infections sums the number of infected people who went to a brunch. 
By summing up the two, the total number of infections at weddings or brunch is calculated and used to find the proportion. 
The similarly it finds the number of infected and traced individuals at each event type and finds the proportion of traced infections from  the event. These proprtions are then used to determine the relative impact of each event type in terms of both infections and tracing effectiveness.
Lastly, results run the simulate_event(m) 50000 times and puts it in props_df dataframe with columns for infection and traced. This helps plot the results. 

The results from the simulation will not match the ones on the blogpost because there wasn't a random seed set in this script. As a consequence the results will be different each time the script is executed, this is because of all the random process used in the model. The random number generators will generate a different number each time so the results will not be reproducible without setting a seed. 

To make the output reporducible we set a random seed, np.random.seed(42), we also change the number of times the simulation is run to 1000. The model will now produce the same output each time it is run, and it will take less time to execute since we reduced the number of simulations. Although reducing the number of times it is run will reduce precision, it will still refelct the same general pattern and be faster. For a simple model mimicking the point the blog post was trying to make, 1000 simulations is enough to show that contact tracing produces biased results. 




























|Criteria|Complete|Incomplete|
|--------|----|----|
|Altercation of the code|The code changes made, made it reproducible.|The code is still not reproducible.|
|Description of changes|The author explained the reasonings for the changes made well.|The author did not explain the reasonings for the changes made well.|

## Submission Information

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

### Submission Parameters:
* Submission Due Date: `HH:MM AM/PM - DD/MM/YYYY`
* The branch name for your repo should be: `sampling-and-reproducibility`
* What to submit for this assignment:
    * This markdown file (sampling_and_reproducibility.md) should be populated.
    * The `whitby_covid_tracing.py` should be changed.
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sampling/pull/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `sampling-and-reproducibility`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via our Slack at `#cohort-3-help`. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.

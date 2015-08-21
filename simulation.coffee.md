---
title: Democratic Equality
author: Dave Kinkead
email: d.kinkead@uq.edu.au
status: bat-shit-crazy idea
license: CC BY SA
---

# Modelling Democratic Equality

In this paper I want to show that egalitarian justifications of democratic authority fail under normal conditions because they cannot reliably produce egalitarian outcomes.

Democracy requires equality.  Political equality, along with popular sovereignty, are essential the notion of democracy itself.

But _equality_ is one of those essentially contested terms and a great deal hinges on exactly which conception of equality democracy requires.  We can think of equality _procedurally_ or _substantively_.

Procedural equality, the idea that equality means treating relevant people the same, is too weak a concept to do the necessary normative work of legitimating democracy.

Substantive equality, the idea that equality means relevant sameness in the thing being realised, is a far more plausible candidate for justifying democracy.

I'm going to create a computer model of a simple democracy with procedural equality and measure it's substantive equality for a range of parameters.

The model consists of agents who hold stipulated preferences `A`, `B`, `C`, `D`, etc, and who all exist in a single polity.


    class Agent
      constructor: (@preference) ->


We create the simulation by populating it with agents and randomly assigning them a preference from a given set of parameters.  


    populate = (parameters) ->
      preferences(parameters).map (preference) ->
        new Agent preference


For now, we will generate a uniform random distribution of preferences.  Each preference has an equiprobable chance of being selected, and over 1000 agents, these should be approximately even.

    
    preferences = (parameters) ->
      [1..parameters.agents].map (agent) ->
        unformlyDistribute parameters.preferences
        
    unformlyDistribute = (preferences) ->
      preferences[Math.floor Math.random() * preferences.length]


Next, we model some democratic processes that have procedural equality:

  - super majority voting
  - plurality voting
  - sortition

In the polling processes, agents honestly express their preference.  


    poll = (agents) ->
      agents.map (agent) ->
            agent.preference
          .reduce (results, preference) ->
            results[preference] = ++results[preference] || 1
            results
          , results={}


In the voting processes, agents have an equal vote and vote naively according to their preference.  We take the results from the prefernce poll and determine the pluarlity winner.  In the case of a tie, we select the first result from the randomly ordered winners.


    plurality = (preferences) ->
      highest = Math.max.apply null, Object.keys(preferences).map (p) ->
        preferences[p]
      winner = Object.keys(preferences).filter (p) ->
          preferences[p] is highest        
      winner[0]


Under sortition, agents have an equiprobable chance of having their preference selected.


    sortition = (agents) ->
      agents[Math.floor Math.random() * agents.length].preference


We then measure the substantive equality that the democratic processes realise over a range of parameter variable in a Monte Carlo simulation with 1000 trials.  In each trial, agents are created with preferences based on stipulated input parameters, before voting or casting lots as modelled above.


    run = (parameters) ->
      simulation = [1..parameters.trials].map (trial) ->
        agents = populate parameters
        {
          "sortition": measure(sortition(agents), agents), 
          "plurality": measure(plurality(poll agents), agents)
        }

      results = {"sortition": [], "plurality": []}
      simulation.map (trial) ->
        ["sortition", "plurality"].map (key) ->
          trial[key].map (score, i) ->
            results[key][i] = 0 if results[key][i] is undefined
            results[key][i] += score    
      results


The outcome of the decision procedure is recorded, along with which agents had their preferences realised by the procedure.  This process of agent creation, decision, and measurement is repeated 1000 times to generate a probability density function with frequency based likelihoods that any agent will have their preference realised by a given procedure.


    measure = (winner, agents) ->
      agents.map (agent) ->
        if agent.preference is winner then 1 else 0


Substantive equality is measured by calculating the coefficient of variation of the likelihood that an agent's preferences will be realised by a decision process.  The idea here is that a process is substantively equal (SD is low) whenever agents have equiprobable preference realisation.  When it is likely that some agents rather than others will have their preferences realised (SD is high), then a process has little substantive equality.  We also normalise it against the mean so that these are comparable.


    sum      = (arr) -> arr.reduce (a,b) -> a + b
    mean     = (arr) -> sum(arr) / arr.length
    variance = (arr) ->
      µ = mean(arr)
      (arr.reduce ( (a,b) -> a + (µ-b)*(µ-b)), 0) / arr.length
    stdev    = (arr) -> Math.sqrt(variance arr)

    equality = (realisations) ->
      stdev(realisations) / mean(realisations)
      


The first parameter value examined is preference distribution.  We start with a uniformly random distribution of preferences across agents ie. roughly equal numbers of each preference being held.  We then `skew` the distribution of preferences so that more of one preference is common within our _digital demos_.

Let's try and visualise this idea using 10 agents and 5 trials.

    # D B A B B A B C C C 
    # D C C B A B A D D A 
    # B C A C B C C D B A 
    # B D D A B C A C D D 
    # C D C C A B A D B C 


    console.log poll(populate {"agents": 1000, "preferences": ["A", "B", "C", "D"]})

> The expectation here is that when preferences are evenly distributed, all three procedures will realise substantive equality.  When preferences are skewed, however, we will (hopefully) see voting but not sortition result in persistent minorities, and thus not realise substantive equality.

> Now we have a criteria for saying when sortition is better than majority voting.

Sometimes, we will want to see what happens when preferences are evenly distributed.  Othertimes, however, we want to know what happens when they are highly skewed.  Depending on the skew parameter that ranges from `0.0` for no skew and `1.0` for higly skewed, we will distribute agent preferences uniformly or exponentially.

We can rewrite the preferences function to generate either uniform or exponential distributions of preferences.  We generate a random number, and if it is less than the `skew` value we use an exponential distribution.


    preferences = (parameters) ->
      [1..parameters.agents].map (agent) ->
        if Math.random() < parameters.skew
            exponentiallyDistribute parameters.preferences
        else
            unformlyDistribute parameters.preferences
      

    exponentiallyDistribute = (preferences) ->
      m = Math.random()
      index = [0..preferences.length-1].filter (n) ->
          pos = (2**(preferences.length - n) - 1) / (2**(preferences.length) - 1)
          m < pos
        .pop()
      preferences[index]

    console.log poll(populate {"agents": 1000, "preferences": ["A", "B", "C", "D"], "skew": 0.25})

Next, we look at preference distribution over time.  Preferences on distinct issues can be consistent or independent.  `Consistency` in preferences here means that if an agent has preference `A` on issue `1`, then they will likely have preference `A` for issue `2`.  We repeat our simulation with both a range of `skew` and `consistency` values.


Let's try and visualise this idea using 10 agents and 5 trials.

    # D D D D D A B C C C 
    # D C A A A A A D D A 
    # B C C C C C C D B A 
    # B B B B B C A C D D 
    # C C C C A B A D B C 


> The expectation now is that when preferences are consistently skewed, persistent minorities will be present in all procedures.  None of them will realise substantive equality in these circumstances.

Finally, we add influence to the model.  By influence I mean informal political power (the ability to chance other's preferences) as opposed to formal power (the right to vote).  We run the simulation again with influence values ranging from uniform to highly concentrated.


    # A A A C A B D C B D C C D A B A A A D B C C C A D B B C B D A C A D D C A C A C A D A B B D D A D A

> The expectation here is that influence will undermine substantive equality in even the non-skewed voting processes.  I'm really not sure what will happen for sortition.

> Also for debate here is whether we should treat the influence as resulting in enlightened preference or are others misled. The former requires measuring against final preference while the latter against initial preference.


Will this be enough to seriously challenge egalitarian justifications of democracy? I have a strong hunch that it will undermine the application of these accounts to our actual democracies that use voting rather than sorting and have concentrated informal power structures.

## Appendix

A number of helper functions are necessary for the simulation to work as an independent program.  As these are not germane to the central argument, the have been extracted from the paper's body to here.  

First let's define some simple statistical functions.
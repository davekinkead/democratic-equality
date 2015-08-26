# Dialectic

1) Some justifications of democracy are egalitarian.  According to these accounts, democracy has value because it embodies political equality.  

Singer, for example, claims that we all want to be dictators - we all want to get our own way.  But we call can't get our own way all the time - an equal share of political power is the only distribution of power that we would agree to (if we are rational that is).  Democracy does just this (uniquely), so it is the only system of government that is justifiable.

But not just Singer - Ronald Dworkin, Joshua Cohen, & Tom Christiano have all advanced similar accounts.  The argument in these accounts all go something like this:

  - if democracy brings about political equality, then it is justified.
  - democracy does bring about political equality.
  - therefore democracy is justified.

2) I want to challenge premise 2.  I am going to argue that it is impossible for democracy, at least under 'normal' conditions, to realise political equality.  And I'm going to do this with computer simulation.

3) _Equality_ is one of those essentially contested terms and a great deal hinges on exactly which conception of equality democracy requires.  One important distinction when considering equality is thinking of it as either _procedurally_ or _substantively_.  

Procedural equality requires treating relevant people the same. It is an agent-blind conception that doesn't make distinctions between agents and treats everyone the same. Substantive equality, by contrast, requires that a process actually realises equality.  It permits treating different people differently in order to achieve equality.  

Yet procedural equality is too weak a concept to do the necessary normative work of legitimating democracy.  Why should we care if a process treats people the same when it doesn't actually _realise_ political equality?  If it is actual equality that matters, then we must focus of substantive equality.

The relationship I'm going to explore then is when does procedural equality realise substantive equality, and under what conditions.

4) Model time.  The model consists of agents who hold stipulated preferences `A`, `B`, `C`, `D`, etc, and who all exist in a single polity.

First, we model 2 democratic processes that have procedural equality:

  - majority voting
  - sortition

We know that both these processes are procedurally equal because we explicitly encode this.  If each agent is treated similarly, then it is procedurally equal. 

We then measure the substantive equality that the democratic processes realise over a range of parameter variable in a Monte Carlo simulation with 1000 trials.  

Substantive equality is measured by calculating the standard deviation of the likelihood that an agent's preferences will be realised by a decision process.  The idea here is that a process is substantively equal (SD is low) whenever agents have equiprobable preference realisation.  When it is likely that some agents rather than others will have their preferences realised (SD is high), then a process has little substantive equality.

In each trial, agents are created with preferences based on stipulated input parameters, before voting or casting lots as modelled above.  We record the outcome of the decision procedure, along with which agents had their preferences realised by the procedure.  This process of agent creation, decision, and measurement is repeated 1000 times to generate a probability density function with frequency based likelihoods that any agent will have their preference realised by a given procedure.

5) Results.

The first parameter value examined is preference distribution.  We start with a uniformly random distribution of preferences across agents ie. roughly equal numbers of each preference being held.  We then `skew` the distribution of preferences so that more of one preference is common within our _digital demos_.

> The expectation here is that when preferences are evenly distributed, all three procedures will realise substantive equality.  When preferences are skewed, however, we will (hopefully) see voting but not sortition result in persistent minorities, and thus not realise substantive equality.

> Now we have a criteria for saying when sortition is better than majority voting.

Next, we look at preference distribution over time.  Preferences on distinct issues can be consistent or independent.  `Consistency` in preferences here means that if an agent has preference `A` on issue `1`, then they will likely have preference `A` for issue `2`.  We repeat our simulation with both a range of `skew` and `consistency` values.

> The expectation now is that when preferences are consistently skewed, persistent minorities will be present in all procedures.  None of them will realise substantive equality in these circumstances.

Finally, we add influence to the model.  By influence I mean informal political power (the ability to chance other's preferences) as opposed to formal power (the right to vote).  We run the simulation again with influence values ranging from uniform to highly concentrated.

> The expectation here is that influence will undermine substantive equality in even the non-skewed voting processes.  I'm really not sure what will happen for sortition.

> Also for debate here is whether we should treat the influence as resulting in enlightened preference or are others misled. The former requires measuring against final preference while the latter against initial preference.

Will this be enough to seriously challenge egalitarian justifications of democracy? I have a strong hunch that it will undermine the application of these accounts to our actual democracies that use voting rather than sorting and have concentrated informal power structures.
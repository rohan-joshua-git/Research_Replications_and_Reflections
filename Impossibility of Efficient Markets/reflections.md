# Grossman and Stiglitz (1980): On the Impossibility of Informationally Efficient Markets

## The core paradox

The efficient markets hypothesis says that prices always reflect all available information. Grossman and Stiglitz point out a problem with taking that literally. Finding good information costs money and effort. If prices already reflected everything, nobody could make a profit from digging up information, so nobody would bother digging it up. But if nobody bothers, there is no mechanism left to make prices reflect that information in the first place.

So you cannot have a market that is both perfectly efficient and full of people who profit from costly research. Something has to give. Their answer is that real markets settle into a state of partial efficiency. Prices reflect some information, not all of it, and that gap is exactly what keeps paying informed traders for their effort.

## Setting up the model

They split the future return of a risky asset, u, into two parts.

$$u = \theta + \varepsilon$$

Theta is the part you can learn if you pay a cost c to research it. Epsilon is pure noise that nobody can predict no matter how much they research. There are two types of traders. Informed traders pay the cost and observe theta directly. Uninformed traders only see the market price and try to infer what they can from it.

The reason the price never fully reveals theta is that the supply of the risky asset is also random and not observable by uninformed traders. This random supply is not explained in depth by the model itself, it is treated as an outside force, but the natural way to think about it is as trading that has nothing to do with information. Some people buy or sell because they need cash, because they are rebalancing a portfolio for unrelated reasons, or because of shifts in how much of the asset happens to be available to trade, not because they learned anything about the asset's true worth. In the literature this kind of trader is usually called a noise trader or liquidity trader. When uninformed traders see the price move, they cannot tell whether that happened because informed traders learned something real, or because this unrelated, uninformed trading randomly shifted supply. That ambiguity is what keeps the price a noisy signal instead of a perfect one.

Both types of traders choose how much of the risky asset to hold by weighing the expected extra return against the risk involved, scaled by how risk averse they are. Informed traders base this on theta, uninformed traders base it on whatever they can read out of the price. An informed trader's edge shows up as extra expected return per unit of leftover, unpredictable risk.

## How the equilibrium works

The market has a feedback loop at its center. The more people who become informed, the more their trading moves prices in response to real information, so the price becomes a better signal. But a better signal means uninformed traders can free ride more effectively off the price, which shrinks the advantage that informed traders were getting paid for. So being informed gets less attractive exactly as more people become informed.

This settles at a stable point where nobody wants to switch. If being informed paid off more than being uninformed, more people would pay for information until that edge disappeared. If it paid off less, informed traders would drop out until being informed became worthwhile again. The fraction of people who end up informed, and how informative prices end up being, are both determined together at this resting point, not fixed in advance.

The authors solve this explicitly in a version of the model with a specific, standard type of risk preference and normally distributed returns. In that solvable version, they show the equilibrium price is literally just theta plus a noise term built from the random supply, scaled down as more traders become informed. This means observing the price is mathematically the same as observing a garbled, noisy version of theta.

## What determines how informative prices are

In the solvable model, the informativeness of prices depends cleanly on three things: the cost of information, the quality of the information itself, and how risk averse traders are. Some results are intuitive: cheaper or better information makes prices more informative, and less risk averse traders push prices to reflect more of what they know.

A more surprising result is that certain kinds of extra randomness in the market, like more noise in asset supply or more overall uncertainty in the asset's return, do not actually change how informative the price ends up being in equilibrium. More noise makes information more valuable, which pulls more people into becoming informed, and those two effects exactly cancel out. The market absorbs the shock through the number of informed traders rather than through the informativeness of the price itself.

## Why perfectly efficient markets cannot exist

If there is no noise in supply at all, prices would perfectly reveal theta. But then nobody has a reason to pay for information since free riding off the price works just as well. If nobody is informed, though, the price carries no information at all, and now it is very profitable for someone to pay for information, since they would have a real edge that nobody else has. Neither state is stable, so there is no equilibrium in this case at all.

The same breakdown happens if information becomes perfectly precise. The market cannot sustain a situation where price fully reflects costly information, because that situation removes the incentive that made the information get incorporated into the price in the first place. This is the actual meaning of the paper's title. It is not that markets never contain useful information. It is that a market genuinely being perfectly informationally efficient, with information remaining costly, is logically inconsistent.

## Thin markets

Trade happens because people disagree, and in this model disagreement comes from differences in information. When almost everyone is informed, or almost everyone is uninformed, most people share very similar beliefs, so there is not much reason to trade. The paper shows that trading volume shrinks toward zero in these extreme cases, and is highest when the population is genuinely split between informed and uninformed traders. This gives a second, related reason why extremely informative markets are fragile. As a market approaches perfect informational efficiency, it also approaches having no trade at all, since everyone starts agreeing with each other.

## What this means for the efficient markets hypothesis

The paper is not trying to tear down the efficient markets idea, it is trying to correct an oversight in it. Believers in efficient markets sometimes treated costless information as a nice simplifying assumption. Grossman and Stiglitz argue it is actually load bearing. Prices can only fully reflect information if that information is free to get. The moment information costs anything, even a small amount, prices can only partially reflect it, because someone has to be rewarded for going and getting it.

## Reflection

Coming into this paper I understood the efficient markets hypothesis the way it is usually taught, as prices already contain all available information, so there is no point trying to beat the market. What this paper changed for me is realizing that statement quietly assumes information gathering is free, and once you drop that assumption the whole thing becomes self contradictory rather than just approximately true.

What I found most satisfying was how mechanical the resolution is. It is not a hand wavy argument about market psychology, it is a real  equilibrium condition, the same kind of supply equals demand logic used everywhere else in economics, just applied to the market for information itself. People are not choosing to have some noise in prices out of politeness to researchers. The noise exists because without it, the whole incentive structure that produces informative prices in the first place would collapse.

The result about noise not changing informativeness stuck with me the most. My first instinct would have been that more randomness in the world should just make prices worse signals, full stop. Seeing that the market compensates for that automatically, by pulling in more informed traders exactly until the effect is canceled, was a genuinely new way of thinking about how markets self correct along dimensions I would not have thought to check.

The biggest shift for me is in how I would now describe efficient markets to someone else. Not as a claim that prices are always right, but as a claim about a stable amount of wrongness that exists specifically because someone is being paid to keep pushing prices toward being right, and never quite getting there.

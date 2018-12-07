---
layout: page
title: Guide
order: 30
---
## Introduction

To illustrate the use of InstAL and the tools we have developed, we present a full worked example of the [tendering scenario](https://doi.org/10.1007/978-3-319-33570-4_2) that appears in:

InstAL: An Institutional Action Language. Padget, J., Elakehal, E., Li, T. & De Vos, M., 2016, Social Coordination Frameworks for Social Technical Systems. Springer Verlag, Vol. 30. p. 101 124 p. (Law, Governance and Technology Series ).
[https://doi.org/10.1007/978-3-319-33570-4_6](https://doi.org/10.1007/978-3-319-33570-4_6).

## The Tendering Scenario

A request for tenders (RFT) is a formal, structured invitation to suppliers, to bid, to supply products or services. For example, a company or government may put a building project 'out to tender'; that is, publish an invitation for other parties to make a proposal for the building's construction.

The aim of the process is to ensure best supplier possible for the requested service or product, such that no parties having the unfair advantage of separate, prior, closed-door negotiations for the contract. Actors, or stakeholders are: contracting authority, bidders consortium (possibly consisting of several partners), evaluators, publication body, ... etc.

A system for RFT should handle more than one RFT at the same time: i.e. there can be more than one contracting authorities putting out RFT to be fulfilled by different bidder consortia, and possibly advertised by different publication bodies.

A RFT process consists of (at least) the following stages
1. Tender elaboration: decide on terms, conditions, deadlines, etc. for the RFT
1. Publication: publication of the tender and/or distributed to potential bidders
1. Request for information: interested bidders can ask for further information to clarify any uncertainties;
1. Bid preparation
1. Bid submission
1. Bid evaluation and decision: An evaluation team will go through the tenders and decide who will get the contract. Each tender will be checked for compliance and if compliant, then evaluated against the criteria specified in the tender documentation. The tender that offers best value for money will win the business.
1. Notification: When a contract has been awarded, the successful tenderer will be advised in writing of
the outcome. Unsuccessful tenderers are also informed
1. Contract formation: a formal agreement will be required between the successful tenderer and the contracting authority

Some of the norms holding in this scenario are (examples):
* Bids must be submitted before the deadline
* Reviewers have to submit their evaluation on time
* All bids must be written in English
* Bids include at least X and at most Y partners
* Each tender must receive at least Z different bids
* Reviewers and requester cannot participate in any bid consortium
* Bids must be blind

This use case outline is informed by the terminology and description at https://en.wikipedia.org/wiki/Request_for_tender. The InstAL modelling of the use case does not necessarily include all the stages and norms noted above.

## Constructing a Model

InstAL models are built around events, who is permitted/empowered to cause them and the effects they have on institutional states, therefore the informal methodology for model specification is to examine the use case for actors, events and facts that need to be remembered. The events typically become external events in the specification, parameterised by relevant information, such the actors responsible for them, while facts become fluents, again parameterised by relevant information, that are initiated or terminated by (institutional) events subject to the institutional state at the time. Some scenarios contain more than one activity, in which case, it can be appropriate to map each activity to a separate institution and then specify cross-generation and cross-consequence rules to capture the interactions between them.

In the case of the tendering scenario, it is clear there are several phases, each of which we have chosen to map to a separate institutional specification, namely publication, review, decision and notification.Various events can be identified from the scenario description associated with each of the phases, which become external events in the specification. Taking the publication phase as an example (see Figure 3), there are also exceptional events, such as a submission after the deadline, which is captured as a violation event, if an actor does not meet the obligation to submit a bid before the
end of the submission phase. Lastly, an institutional state or condition can be captured through the use of a non-inertial fluent, in this case to signal that a separation of roles has not been observed.

Once a specification comprises several institutions, it becomes necessary to consider how they may affect one another. This aspect is expressed using cross-generation and cross-consequence rules, as illustrated in Figure 4. In particular, the figure shows that the institutional event intSubmissionDue in the publication institution generates the external event reviewStarts in the review institution, with corresponding similar cross-generation rules connecting review and decision and decision and notification. Similarly, an event in one institution can bring about a change of state in another, such as the submission of a bid (intSubmitBid) in publication leading to the initiation of the fluent readForReview in the review institution.

**describe workflow for creating an institution here**

### Specifying the publication phase

An institutions has a name (publication), some types for the parameters to events, and exogenous, institutional and violation events: 
<pre><code>
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% RFT Scenorio -- Publication Phase: 
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

% institution name
institution publication;
% types
type RFT;    
type Agent;    
type Bid;  
type Role;      
type Number; 
type Decision;

% exogenous events 
exogenous event publishRFT(Agent, RFT); 
exogenous event registerBid(Agent, Bid, RFT);
exogenous event submitBid(Agent, Bid, RFT); 
exogenous event notifyReceipt(Agent, Bid, RFT); 
exogenous event receiptDue(Agent, Bid, RFT); 
exogenous event submissionDue(RFT);

% inst events
inst event intPublishRFT(Agent, RFT); 
inst event intRegisterBid(Agent, Bid, RFT);
inst event intSubmitBid(Agent, Bid, RFT); 
inst event intNotifyReceipt(Agent, Bid, RFT); 
inst event intReceiptDue(Agent, Bid, RFT); 
inst event intSubmissionDue(RFT);

% violation events
% used as violation in obl of submission by due 
violation event lateSubmission(Agent, Bid, RFT);
violation event lateReceipt(Agent, Bid, RFT); 
</code></pre>
The state of the institution is represented as a set of fluents. Inertial fluents are added to the institutional state by initiates rule (q.v.), removed from it by terminates rules (q.v), and otherwise persist from state to state, because they are *inertial*. Inertial fluents include domain fluents:
<pre><code>
% fluents
fluent roleOf(Agent, Role); 
fluent bidsReceived(RFT, Number); 
fluent nextNum(Number, Number);  
fluent sufficientBids(RFT);
fluent bidSubmitted(RFT, Bid, Agent); 
</code></pre>
and institutional fluents, such as permissions, powers, which here are declared to be present in the initial institutional state:
<pre><code>
% initially
initially perm(publishRFT(Agent, RFT)),
          perm(intPublishRFT(Agent, RFT)), 
          pow(intPublishRFT(Agent, RFT)); 

initially perm(receiptDue(Agent, Bid, RFT)),
          perm(intReceiptDue(Agent, Bid, RFT)),
          pow(intReceiptDue(Agent, Bid, RFT)); 

initially perm(submissionDue(RFT)),
          perm(intSubmissionDue(RFT)),
          pow(intSubmissionDue(RFT));          

initially roleOf(ting, requester),
          roleOf(alice, bidder),
          roleOf(bob, bidder); 

initially nextNum(n0, n1),  nextNum(n1, n2),  nextNum(n2, n3); 

initially bidsReceived(RFT, n0);
</code></pre>
and obligations, which take the form obl(e,d,v), where e is the event that should happen, by deadline d, or else violation v happens:
<pre><code>
% obligation fluent
obligation fluent obl(submitBid(Agent, Bid, RFT),
	   	                intSubmissionDue(RFT),
		                  lateSubmission(Agent, Bid, RFT)); 
obligation fluent obl(notifyReceipt(Agent, Bid, RFT),
                      intReceiptDue(Agent, Bid, RFT),
                      lateReceipt(Agent, Bid, RFT)); 
</code></pre>
The state may also be augmentd with non-inertial fluents. These are present in the institutional state only if their controlling conditions over the institutional state are satisified. Here they are used to model that an agent may not simultaneously be a bidder and a requester or be a bidder and an evaluator:
<pre><code>
% non-inertial fluents
noninertial fluent violated(Agent);

violated(Agent) when
   roleOf(Agent, bidder), roleOf(Agent, requester); 

violated(Agent) when
   roleOf(Agent, bidder), roleOf(Agent, evaluator); 
</code></pre>
The generates rules correspond to the notion of counts-as, so that an exogenous event brings about an institution event only if it is permitted, empowered **needs revision to account for new semantics** and satisfies any other condition that may be associated with the generation rule. Here the permissions and powers are set up through the initially declarations above, while some of the rules check the the agent role is correct for the agent assocaited with the exogenous event:**need to explain why perm is not right way to do this**
<pre><code>
% generation rules 
publishRFT(Agent, RFT) generates
   intPublishRFT(Agent, RFT)
   if roleOf(Agent, requester); 
registerBid(Agent, Bid, RFT) generates
   intRegisterBid(Agent, Bid, RFT)
   if roleOf(Agent, bidder);
submitBid(Agent, Bid, RFT) generates
   intSubmitBid(Agent, Bid, RFT)
   if roleOf(Agent, bidder); 
submitBid(Agent, Bid, RFT) generates
   intReceiptDue(Agent, Bid, RFT)
   if roleOf(Agent, bidder) in 2; 
notifyReceipt(Agent, Bid, RFT) generates
   intNotifyReceipt(Agent, Bid, RFT); 
submissionDue(RFT) generates
   intSubmissionDue(RFT); 
</code></pre>
<pre><code>
% consequence rules
intPublishRFT(Agent, RFT) initiates
   perm(registerBid(Agent1, Bid, RFT)), 
   perm(intRegisterBid(Agent1, Bid, RFT)),
   pow(intRegisterBid(Agent1, Bid, RFT))
   if roleOf(Agent1, bidder);

intRegisterBid(Agent, Bid, RFT) initiates
   perm(submitBid(Agent, Bid, RFT)) ,
   perm(intSubmitBid(Agent, Bid, RFT)),
   pow(intSubmitBid(Agent, Bid, RFT)),
   obl(submitBid(Agent, Bid, RFT),
       intSubmissionDue(RFT),
       lateSubmission(Agent, Bid, RFT));

intSubmitBid(Agent, Bid, RFT) initiates
   perm(notifyReceipt(Agent, Bid, RFT)), 
   perm(intNotifyReceipt(Agent, Bid, RFT)),
   pow(intNotifyReceipt(Agent, Bid, RFT)); 

intSubmitBid(Agent, Bid, RFT) initiates
   bidSubmitted(RFT, Bid, Agent); 

intSubmitBid(Agent, Bid, RFT) initiates
   bidsReceived(RFT, Number2)
   if nextNum(Number1, Number2), bidsReceived(RFT, Number1);

intSubmitBid(Agent, Bid, RFT) terminates
   bidsReceived(RFT, Number)
   if bidsReceived(RFT, Number);

intSubmitBid(Agent, Bid, RFT) initiates
   sufficientBids(RFT)
   if bidsReceived(RFT, n1); 

intSubmitBid(Agent, Bid, RFT) initiates
   obl(notifyReceipt(Agent, Bid, RFT),
       intReceiptDue(Agent, Bid, RFT),
       lateReceipt(Agent, Bid, RFT)); 

intSubmissionDue(RFT) terminates
   perm(registerBid(Agent, Bid, RFT)), 
   perm(intRegisterBid(Agent, Bid, RFT)),
   pow(intRegisterBid(Agent, Bid, RFT)),
   perm(submitBid(Agent, Bid, RFT)),
   perm(intSubmitBid(Agent, Bid, RFT)),
   pow(intSubmitBid(Agent, Bid, RFT)); 
</code></pre>

### Review

### Decision

### Notification

### Bridge

## A Sample Trace

## A State Visualization

## An Event Visualization

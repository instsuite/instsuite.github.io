---
title: "Modelling GDPR"
permalink: "/gdpr/"
layout: page
---

## Modelling parts of the GDPR in InstAL

Descriptive text to be added. For now, there is only the model.

```yaml
institution gdpr;

type Article;
type Object;
type Predicate;
type Subject;

% encoding methodology: map a RDF triple <S,P,O> to a term triple(S,P,O)

fluent triple(Subject,Predicate,Object);

% EXAMPLE 11/12: see ex12.iaf
% <http://example.com/policy:01> a orcp:Set ;
%     odrl:profile <http://example.com/odrl:profile:regulatory-compliance> .
%     orcp:request 
% 	[ odrl:action orcp:Processing ;
%           orcp:resource orcp:PersonalData ;
%           orcp:controller <http://example.com/CompanyA> ;
%           orcp:purpose orcp:KYC ;
%           orcp:location orcp:EU ;
% 	  orcp:legalBasis orcp:Consent ; 
% 	  odrl:responsibleParty orcp:Controller
%         ] ;

% EXAMPLE 13/14: see ex14.iaf
% <http://example.com/policy:01> a orcp:Set ;
%     odrl:profile <http://example.com/odrl:profile:regulatory-compliance> .
%     orcp:request 
% 	[ odrl:action orcp:Processing ;
%           orcp:resource orcp:PersonalData ;
%           orcp:controller <http://example.com/CompanyA> ;
%           orcp:purpose orcp:KYC ;
%           orcp:location orcp:EU ;
% 	  odrl:responsibleParty orcp:Controller
%         ] ;
	
% EXAMPLE 17/18: see ex18.iaf
% <http://example.com/policy:01> a orcp:Set ;
%     odrl:profile <http://example.com/odrl:profile:regulatory-compliance> .
%     orcp:request 
% 	[ odrl:action orcp:Transfer ;
%           orcp:resource orcp:PersonalData ;
%           orcp:responsibleParty orcp:Controller ;          
% 	  orcp:organisationType orcp:InternationalOrganisation ;
%           orcp:sender <http://example.com/TR_Ireland> ;
%           orcp:recipient <http://example.com/TR_USA> ;
% 	  orcp:recipientLocation orcp:ThirdCountry ;
%           orcp:purpose orcp:KYC ;
%           orcp:legalBasis orcp:Consent ;
%           odrl:dataSubjectProvisions orcp:EnforceableDataSubjectRights ;
%           odrl:dataSubjectProvisions orcp:LegalRemediesForDataSubjects ;
% 	  orcp:appropriateSafeguards orcp:BindingCorporateRules 
%         ] ;


% EXAMPLE 19/20: see ex20.iaf
% <http://example.com/policy:01> a orcp:Set ;
%     odrl:profile <http://example.com/odrl:profile:regulatory-compliance> .
%     orcp:request 
% 	[ odrl:action orcp:Transfer ;
%           orcp:resource orcp:PersonalData ;
%           orcp:responsibleParty orcp:Controller ;          
% 	  orcp:organisationType orcp:InternationalOrganisation ;
%           orcp:sender <http://example.com/TR_Ireland> ;
%           orcp:recipient <http://example.com/TR_USA> ;
% 	  orcp:recipientLocation orcp:ThirdCountry ;
%           orcp:purpose orcp:KYC ;
%           orcp:legalBasis orcp:Consent ;
%           odrl:dataSubjectProvisions orcp:EnforceableDataSubjectRights ;
%           odrl:dataSubjectProvisions orcp:LegalRemediesForDataSubjects 
%         ] ;

exogenous event doRequest(Subject);
inst event _doRequest(Subject);
initially perm(doRequest(S)), pow(doRequest(S)), perm(_doRequest(S));
fluent permission(Subject,Article);
fluent prohibition(Subject,Article);
noninertial fluent supports(Subject,Article,Predicate,Object);
noninertial fluent lacks(Subject,Article,Predicate,Object);
noninertial fluent applies(Subject,Article);
applies(Request,article6) when triple(Request,action,processing);
applies(Request,article46) when triple(Request,action,transfer);

% Article 6
% http://example.com/policy:para1 odrl:action orcp:Processing ;
%     orcp:resource orcp:PersonalData ;
%     odrl:predicateConstraint 
% 	[ odrl:and ( 
% 		[ odrl:leftOperand orcp:responsibleParty ;
%                   odrl:operator odrl:isAnyOf ;
%                   odrl:rightOperand ( orcp:Controller 
% 				      orcp:Processor ) 
%                 ] 
%                 [ odrl:leftOperand orcp:legalBasis ;
%                   odrl:operator odrl:isAnyOf ;
%                   odrl:rightOperand ( orcp:Consent 
%                                       orcp:Contract 
%                                       orcp:LegalObligation 
%                                       orcp:VitalInterest 
%                                       orcp:PublicInterest
%                                       orcp:ExerciseOfOfficialAuthority 
%                                       orcp:LegitimateInterest ) 
%                 ] ) 
%          ] .

% check if predicate holds for more than one object
noninertial fluent article6_isSomeOf_responsibleParty(Subject);
article6_isSomeOf_responsibleParty(Request) when
   triple(Request,responsibleParty,X),
   triple(Request,responsibleParty,Y),
   X!=Y;

noninertial fluent article6_isAnyOf_responsibleParty(Subject);
noninertial fluent article6_responsibleParty(Subject);

article6_isAnyOf_responsibleParty(Request) when
   article6_responsibleParty(Request),
   not article6_isSomeOf_responsibleParty(Request),
   triple(Request,responsibleParty,X);
% replicate rhs because when does not support more than one lhs fluent
supports(Request,article6,responsibleParty,X) when
   article6_responsibleParty(Request),
   not article6_isSomeOf_responsibleParty(Request),
   triple(Request,responsibleParty,X);
lacks(Request,article6,responsibleParty,controller) when
   applies(Request,article6),
   % not supports(Request,article6,responsibleParty,controller);
   not article6_responsibleParty(Request);
lacks(Request,article6,responsibleParty,processor) when
   applies(Request,article6),
   % not supports(Request,article6,responsibleParty,processor);
   not article6_responsibleParty(Request);

% article 6 responsibleParty cases
article6_responsibleParty(Request) when
   triple(Request,responsibleParty,controller);
article6_responsibleParty(Request) when
   triple(Request,responsibleParty,processor);

% check if predicate holds for more than one object
noninertial fluent article6_isSomeOf_legalBasis(Subject);
article6_isSomeOf_legalBasis(Request) when
   triple(Request,legalBasis,X),
   triple(Request,legalBasis,Y),
   X!=Y;

noninertial fluent article6_isAnyOf_legalBasis(Subject);
noninertial fluent article6_legalBasis(Subject);
noninertial fluent not_article6_legalBasis(Subject);

article6_isAnyOf_legalBasis(Request) when
   article6_legalBasis(Request),
   not article6_isSomeOf_legalBasis(Request),
   triple(Request,legalBasis,X);
% replicate rhs because when does not support more than one lhs fluent
supports(Request,article6,legalBasis,X) when
   article6_legalBasis(Request),
   not article6_isSomeOf_legalBasis(Request),
   triple(Request,legalBasis,X);
lacks(Request,article6,legalBasis,consent) when
   applies(Request,article6),
   not article6_legalBasis(Request);
lacks(Request,article6,legalBasis,contract) when
   applies(Request,article6),
   not article6_legalBasis(Request);
lacks(Request,article6,legalBasis,legalObligation) when
   applies(Request,article6),
   not article6_legalBasis(Request);
lacks(Request,article6,legalBasis,vitalInterest) when
   applies(Request,article6),
   not article6_legalBasis(Request);
lacks(Request,article6,legalBasis,publicInterest) when
   applies(Request,article6),
   not article6_legalBasis(Request);
lacks(Request,article6,legalBasis,legitimateInterest) when
   applies(Request,article6),
   not article6_legalBasis(Request);

% article 6 legalBasis cases
article6_legalBasis(Request) when
   triple(Request,legalBasis,consent);
article6_legalBasis(Request) when
   triple(Request,legalBasis,contract);
article6_legalBasis(Request) when
   triple(Request,legalBasis,legalObligation);
article6_legalBasis(Request) when
   triple(Request,legalBasis,vitalInterest);
article6_legalBasis(Request) when
   triple(Request,legalBasis,publicInterest);
article6_legalBasis(Request) when
   triple(Request,legalBasis,exerciseOfficialAuthority);
article6_legalBasis(Request) when
   triple(Request,legalBasis,legitimateInterest);

noninertial fluent article6(Subject);
article6(Request) when
   % triple(Request,action,processing),
   article6_isAnyOf_responsibleParty(Request),
   article6_isAnyOf_legalBasis(Request)
;

% Article 46
% <http://example.com/policy:03> a orcp:Set ;
%     odrl:profile <http://example.com/odrl:profile:regulatory-compliance> .
%     orcp:permission 
% 	[ odrl:action orcp:Transfer ;
% 	  orcp:resource orcp:PersonalData ;
%           odrl:predicateConstraint 
% 		[ odrl:or ( 
%                         [ odrl:leftOperand orcp:organisationType ;
%                           odrl:operator odrl:eq ;
%                           odrl:rightOperand orcp:InternationalOrganisation 
%                         ] 
%                         [ odrl:leftOperand orcp:recipientLocation ;
%                           odrl:operator odrl:eq ;
%                           odrl:rightOperand orcp:ThirdCountry 
%                         ] ) 
% 		] 
%           orcp:obligation 
% 		[ orcp:resource orcp:EnforceableDataSubjectRights ;
%                   odrl:action orcp:verifyExistanceOf
%                   odrl:predicateConstraint 
% 			[ odrl:leftOperand orcp:dataSubjectProvisions ;
%                           odrl:operator odrl:eq ;
%                           odrl:rightOperand "orcp:EnforceableDataSubjectRights" 
%                         ] 
%                 ],
%                 [ orcp:resource orcp:LegalRemediesForDataSubjects ;
%                   odrl:action orcp:verifyExistanceOf 
%                   odrl:predicateConstraint 
% 			[ odrl:leftOperand orcp:dataSubjectProvisions ;
%                           odrl:operator odrl:eq ;
%                           odrl:rightOperand "orcp:LegalRemediesForDataSubjects" 
%                         ] 
% 		],
%                 [ orcp:resource orcp:AppropriateSafeguards ;
%                   odrl:action orcp:verifyExistanceOf ;
%                   odrl:predicateConstraint 
% 			[ odrl:leftOperand orcp:appropriateSafeguards ;
%                           odrl:operator odrl:isAnyOf ;
%                           odrl:rightOperand ( orcp:LegallyBindingEnforceableInstrument 
% 					      orcp:BindingCorporateRules 
%                                               orcp:DataProtectionClauses 
% 					      orcp:ApprovedCodeOfConduct  
%                                               orcp:ApprovedCertificateMechanism ) 
%                         ] 
% 		] 
% 	] ;

% check that one or another or both organisationTypes hold(s)
noninertial fluent article46_or_organisationType(Subject);
noninertial fluent article46_organisationType(Subject);
noninertial fluent article46_recipientLocation(Subject);

article46_or_organisationType(Request) when
   article46_organisationType(Request);
supports(Request,article46,organisationType,X) when
   article46_or_organisationType(Request),
   triple(Request,organisationType,X);
lacks(Request,article46,organisationType,internationalOrganisation) when
   % note conjunct in lacks because subject is a disjunction
   applies(Request,article46),
   not article46_organisationType(Request),
   not article46_recipientLocation(Request);
article46_or_organisationType(Request) when
   article46_recipientLocation(Request);
supports(Request,article46,recipientLocation,X) when
   article46_or_organisationType(Request),
   triple(Request,organisationLocatedIn,X);
lacks(Request,article46,recipientLocation,thirdCountry) when
   applies(Request,article46),
   % note conjunct in lacks because subject is a disjunction
   not article46_organisationType(Request),
   not article46_recipientLocation(Request);

article46_organisationType(Request) when
   triple(Request,organisationType,internationalOrganisation);
article46_recipientLocation(Request) when
   triple(Request,recipientLocation,thirdCountry);

% check if predicate holds for more than one object
noninertial fluent article46_someOf_appropriateSafeguards(Subject);
article46_someOf_appropriateSafeguards(Request) when
   triple(Request,appropriateSafeguards,X),
   triple(Request,appropriateSafeguards,Y),
   X!=Y;

% generic version of above seems to slow grounding considerably:
% product of Predicate and Object?
% check if predicate holds for more than one object
% noninertial fluent someOf(Subject,Predicate);
% someOf(Request,Predicate) when
%    triple(Request,Predicate,X),
%    triple(Request,Predicate,Y),
%    X!=Y;

% check that exactly one appropriateSafeguard holds
% until clear whether isAnyOf = isOneOf or isSomeOf
noninertial fluent article46_isAnyOf_appropriateSafeguards(Subject);
noninertial fluent article46_appropriateSafeguards(Subject);

article46_isAnyOf_appropriateSafeguards(Request) when
   article46_appropriateSafeguards(Request),
   % not someOf(Request,appropriateSafeguards),
   not article46_someOf_appropriateSafeguards(Request),
   triple(Request,appropriateSafeguards,X);
% replicate rhs because when does not support more than one lhs fluent
supports(Request,article46,appropriateSafeguards,X) when
   article46_appropriateSafeguards(Request),
   not article46_someOf_appropriateSafeguards(Request),
   triple(Request,appropriateSafeguards,X);
lacks(Request,article46,appropriateSafeguards,bindingCorporateRules) when
   applies(Request,article46),
   not article46_appropriateSafeguards(Request);
lacks(Request,article46,appropriateSafeguards,legallyBindingEnforceableInstruments) when
   applies(Request,article46),
   not article46_appropriateSafeguards(Request);
lacks(Request,article46,appropriateSafeguards,standardDataProtectionClauses) when
   applies(Request,article46),
   not article46_appropriateSafeguards(Request);
lacks(Request,article46,appropriateSafeguards,approvedCodeOfConduct) when
   applies(Request,article46),
   not article46_appropriateSafeguards(Request);
lacks(Request,article46,appropriateSafeguards,approvedCertificateMechanism) when
   applies(Request,article46),
   not article46_appropriateSafeguards(Request);

% article 46 appropriateSafeguards cases
article46_appropriateSafeguards(Request) when
   triple(Request,appropriateSafeguards,bindingCorporateRules);
article46_appropriateSafeguards(Request) when
   triple(Request,appropriateSafeguards,legallyBindingEnforceableInstrument);
article46_appropriateSafeguards(Request) when
   triple(Request,appropriateSafeguards,standardDataProtectionClauses);
article46_appropriateSafeguards(Request) when
   triple(Request,appropriateSafeguards,approvedCodeOfConduct);
article46_appropriateSafeguards(Request) when
   triple(Request,appropriateSafeguards,approvedCertificateMechanism);

noninertial fluent article46_body_term1(Subject);
article46_body_term1(Request) when
  article46_or_organisationType(Request);
noninertial fluent article46_body_term2(Subject);
article46_body_term2(Request) when
  triple(Request,dataSubjectProvisions,enforceableDataSubjectRights);
supports(Request,article46,dataSubjectProvisions,enforceableDataSubjectRights) when
  article46_body_term2(Request),
  triple(Request,dataSubjectProvisions,enforceableDataSubjectRights);
lacks(Request,article46,dataSubjectProvisions,enforceableDataSubjectRights) when
   applies(Request,article46),
   not supports(Request,article46,dataSubjectProvisions,enforceableDataSubjectRights);
noninertial fluent article46_body_term3(Subject);
article46_body_term3(Request) when
  triple(Request,dataSubjectProvisions,effectiveLegalRemedies);
supports(Request,article46,dataSubjectProvisions,effectiveLegalRemedies) when
  article46_body_term3(Request),
  triple(Request,dataSubjectProvisions,effectiveLegalRemedies);
lacks(Request,article46,dataSubjectProvisions,effectiveLegalRemedies) when
   applies(Request,article46),
   not supports(Request,article46,dataSubjectProvisions,effectiveLegalRemedies);
noninertial fluent article46_body_term4(Subject);
article46_body_term4(Request) when
  article46_isAnyOf_appropriateSafeguards(Request);
noninertial fluent article46_body(Subject);
article46_body(Request) when
  article46_body_term1(Request),
  article46_body_term2(Request),
  article46_body_term3(Request),
  article46_body_term4(Request)  
;

noninertial fluent article46(Subject);
article46(Request) when
  % triple(Request,action,transfer),
  triple(Request,resource,personalData),
  article46_body(Request)
  % article46_or_organisationType(Request), 
  % triple(Request,dataSubjectProvisions,enforceableDataSubjectRights),
  % article46_isAnyOf_appropriateSafeguards(Request)
;

doRequest(Request) generates _doRequest(Request);
_doRequest(Request) initiates
   permission(Request,article6)
   if article6(Request), triple(Request,action,processing);
_doRequest(Request) initiates
   prohibition(Request,article6)
   if not article6(Request), triple(Request,action,processing);
_doRequest(Request) initiates
   permission(Request,article46)
   if article46(Request), triple(Request,action,transfer);
_doRequest(Request) initiates
   prohibition(Request,article46)
   if not article46(Request), triple(Request,action,transfer);

```

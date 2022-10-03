# Market DAO


*I fear that if a well designed free market can't defeat [Moloch](https://slatestarcodex.com/2014/07/30/meditations-on-moloch/), nothing can and we're all fucked.*

*Which is to say, "I fear we're all fucked."*

*That said, May as well go down fighting.  If nothing else, it's more fun than the alternative.*

Market DAO is my idea of how we go down fighting.  I really think this thing can be tuned to provide incentives appropriate to the betterment of humanity and its environs.  Well, at least it's worth a shot...

The DAO itself is based on two principles:

- A system of structured attestations which is used periodically to produce rewards in the form of fungible tokens.
- A market based voting system.

In order to explain how all this works, we'll need to start with some rigorous(ish) definitions, as the terms standard English offers are confusingly nebulous.

- **Voting Event** -  An event in which token holders vote (ok, duh, but I need to distinguish it from the act of voting or the contents of a cast vote, or...)
- **Voting Token** - An expiring token good for one vote at one voting event.  Burned at vote date if unused.
- **Permanent Token** - Non expiring reward tokens.  They can represent any value desired, but initially their primary pupose is to emit voting tokens when a voting event is called.  I'm not crazy about this name but it will have to do for now.
- **Attestation** - A statement made by the owner of a wallet about the owner of another wallet (it can actually be the same wallet, but those won't count for much).  **These cannot be transferred**.
- **Structured Attestation** - As the name implies, an attestation with named fields.  
- **Attestation Structure** - A set of field names to be used for structured attestations
- **Attestation Structure Instance** - An attestation structure with values for its fields.  When "making a structured attestation," a wallet holder is actually signing an attestation structure instance.
- **Reward Formula** - This is a formula used to issue reward tokens based on attestations made.  This transform function should be run on a regular basis.  The reward period, and all reward formulas should be subject to change via voting events.

For those of you in the audience counting, yes, there are two different token types referenced above, plus structured attestations, which aren't really tokens but if you squint hard enough they kind of look like semi-fungible tokenoids.

I love coding and hate writing (thoguh I confess it's a lot less painful when you don't have to worry about whether or not some asshole VC will fund your project), so rather than crank out a(nother lousy) whitepaper (no offense to whitepaper writers; I respect your people), I hope the following sequence descriptions will suffice to illustrate, at least at a high level, the major functions of a Market DAO.  

# Event Sequences in the DAO

## DAO Creation

- A random wallet (a.k.a "somebody") creates and deploys Factory Contract.  Some parameters should be set at this point (including min/max voting/market period) but there is no exhaustive list yet.
- The Factory Contract deploys a multi-asset wallet contract, which it owns.
  - The reason the factory contract currently owns the wallet is that it will need to reassign ownership to a voting contract.  There should be no code in the factory contract that alters the wallet in any other way (Gah!  probably; it may make more sense to have the factory create a new wallet for each voting event involving a funds transfer).

## Attestation Structure Creation

- Factory Contract creates an Attestation Structure Contract consisting of a list of field names.
  - Attestation Structure contracts have no owner and all r/w function are available to the public.
  - That said, the field names, once set, are immutable.


## Attestaion Structure Instance Creation

- A specific Attestation Structure Contract creates an instance of itself, consisting of values for its fields
  - Like the structures themselves, structure instances can be created by anyone but once created are immutable.
  - There should probably be functionality here to delete these things as long as no attestations have been made.  Maybe also for Attestation Structures without instances.

## Attestation Creation

- A specific wallet attests that a specific Attestation Structure Instance applies to another wallet
  - Any wallet can make an attestation about any wallet.
  - The plan for the moment is that these cannot be deleted, but of course that can change and allows for variations.

## Reward Formula Vote Proposal Process
**TODO** However: Voting proposals cannot be a free for all.  That would be an enormous headache.  Some ideas to limit proposals (this could be a setting, and could even be changeable by a voting event):
  - Only wallets with more than X tokens can propose a vote.
  - Voting proposals require at least X tokens worth of "support"; functionality would need to be built for this.
  - Voting proposals require support from at least X wallets, each of which contains at least Y tokens.
  - Voting proposals require some kind of bounty which passes into the vault if proposal fails.

Also, it occurs to me that a DSL for reward formula description might be called for here.  Sadly, the Geneva convention forbids writing parsers in Solidity.  Then again, fuck 'em!


## Treasury transfer Vote Proposal Process
**TODO** However:  Such proposals at minimum will need to include target address, asset types and amounts, and type and number of tokens expected.  
- There is an opportunity here for a kill switch in that the proposal can stipulate sth like "if the proposal passes, the outbound transfer happens, and the expected inbound transfer does not, PANIC!" (provided that "PANIC!" is well defnied)

## Reward Award (Periodic and Non-interactive)
- Rewards contract iterates through all active rewards formulas
  - For each formula, it iterates through all the relevant attestations (each reward formula must reference at least one attestation structure instance) and mints the appropriate number of tokens to each attestee (maybe attestor as well).

## Voting Event
- At the moment, I only have two types of voting events in mind:
  - Reward formula changes
  - Transfers out of the vault
- The procedure seems to me like it should be the same:
  - Proposal made and approved for voting (procedure TBD as per above)
    - Proposals should include a voting/market period. with a minimum and maximum TBD at creation time.
  - One Voting token is issued for every permanent token in every wallet that holds them.
  - Voting token holders are now free to vote, sell their voting tokens, or do nothing.
    - Voting, of course, entails burning a voting token
  - When the voting/market period ends, votes are tallied (this is, of course, non-interactive but the result will be easily knowable several blocks in advance under most circumstances).
    - If the proposal passes, the funds are transferred in the case of a transfer voting event, or the reward formula is activated/deactivated in the event of a reward change voting event.
    - If the proposal does not pass, nothing happens.  As of now, there should be no state to revert, with the possible exception of deleting the proposal.
    - Regardless of which way the voting event goes, all tokens relating to it that have not already been burned are burned at this point having abstained.

---

I have in mind a play in three acts, entitled "DAOnton abbey" which would demonstrate how a market DAO could be used to run a chamber of commerce.  It will probably never get written, but maybe if I mention it here it will push me to actually do it while procrastinating coding.  
## DAO Smart Contract

# Overview

This repository contains the smart contract for a Decentralized Autonomous Organization (DAO) deployed on the Q Blockchain Testnet.


# Features

- **Membership Management:** Add and remove members from the DAO.
- **Proposal System:** Create, vote on, and execute proposals.
- **Transparent Voting:** Members can vote "Yes" or "No" on proposed actions.




# FUNCTIONS USAGE

# Adding a Member

Use the addMember function to add a new member to the DAO.

solidity


```Solidity
function addMember(address _member) public {
            require(msg.sender == owner);
            require(isMember[_member] == false, "Member already exists");
            memberInfo[_member] = Member({
                memberAddress: _member,
                memberSince: block.timestamp,
                tokenBalance: 100
            });
            members.push(_member);
            isMember[_member] = true;
            balances[_member] = 100;
            totalSupply += 100;
        }
		
```


# Removing a Member

Use the removeMember function to remove a member from the DAO

```Solidity
function removeMember(address _member) public {
            require(isMember[_member] == true, "member does not exist");
            memberInfo[_member] = Member({
                memberAddress: address(0),
                memberSince: 0,
                tokenBalance: 0
            });
            for (uint i = 0; i < members.length; i++) {
                if (members[i] == _member) {
                    members[i] = members[members.length - 1];
                    members.pop();
                    break;
                }
            }
            isMember[_member] = false;
            balances[_member] = 0;
            totalSupply -= 100;
        }
```


# Creating a Proposal

Create a new proposal using the createProposal function.

```Solidity
function createProposal(string memory _description) public {
		    proposals.push(Proposal({
		        description: _description,
		        voteCount: 0,
                yesVotes: 0,
                noVotes: 0,
		        executed: false
		    }));
            
		    emit ProposalCreated(proposals.length - 1, _description);
		}

```


# Voting

Members can vote on proposals using the voteYes and voteNo functions.

```Solidity
// Function for a member to vote on a proposal
		function voteYes(uint _proposalId, uint _tokenAmount) public {
            require(isMember[msg.sender] == true, "You should be a memeber to vote");
            require(balances[msg.sender] >= _tokenAmount, "Not enough tokens to vote");
            require(votes[msg.sender][_proposalId] == false, "You have already voted for this proposal");
            votes[msg.sender][_proposalId] = true;
            memberInfo[msg.sender].tokenBalance -= _tokenAmount;
            proposals[_proposalId].voteCount += _tokenAmount;
            proposals[_proposalId].yesVotes += _tokenAmount;


            emit VoteCast(msg.sender, _proposalId, _tokenAmount);
        }		
  
        function voteNo(uint _proposalId, uint _tokenAmount) public {
            require(isMember[msg.sender] == true, "You should be a memeber to vote");
            require(balances[msg.sender] >= _tokenAmount, "Not enough tokens to vote");
            require(votes[msg.sender][_proposalId] == false, "You have already voted for this proposal");
            votes[msg.sender][_proposalId] = true;
            memberInfo[msg.sender].tokenBalance -= _tokenAmount;
            proposals[_proposalId].voteCount += _tokenAmount;
            proposals[_proposalId].noVotes += _tokenAmount;

            emit VoteCast(msg.sender, _proposalId, _tokenAmount);
        }
  
```

# Executing a Proposal

Once a proposal has enough votes, execute it with the executeProposal function.

```Solidity
// Function to execute a proposal in the DAO
        function executeProposal(uint _proposalId) public {
            require(proposals[_proposalId].executed == false, "Proposal has already been executed");
            require(proposals[_proposalId].yesVotes > proposals[_proposalId].noVotes, "Do not have enough votes");
            proposals[_proposalId].executed = true;

            emit ProposalAccepted("Proposal has been approved");
```



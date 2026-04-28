# Escrow-with-Arbitration
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract EscrowWithArbitrator {
    address public buyer;
    address public seller;
    address public arbitrator;
    uint256 public amount;
    bool public fundsReleased;
    bool public disputeOpened;

    event DisputeOpened(address by);
    event FundsReleased(address to);

    constructor(address _seller, address _arbitrator) payable {
        buyer = msg.sender;
        seller = _seller;
        arbitrator = _arbitrator;
        amount = msg.value;
    }

    function release() public {
        require(msg.sender == buyer, "Only buyer");
        require(!disputeOpened, "Dispute opened");
        fundsReleased = true;
        payable(seller).transfer(amount);
        emit FundsReleased(seller);
    }

    function openDispute() public {
        require(msg.sender == buyer || msg.sender == seller, "Not party");
        disputeOpened = true;
        emit DisputeOpened(msg.sender);
    }

    function arbitratorResolve(address to) public {
        require(msg.sender == arbitrator, "Only arbitrator");
        payable(to).transfer(amount);
        emit FundsReleased(to);
    }
}

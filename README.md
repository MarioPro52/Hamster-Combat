# Hamster-Combat// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract HamsterCombat {

    struct Hamster {
        string name;
        uint256 strength;
        uint256 level;
    }

    mapping(address => Hamster) public hamsterCollection;
    mapping(address => uint256) public wins;
    mapping(address => uint256) public losses;

    event HamsterCreated(address indexed owner, string name);
    event HamsterTrained(address indexed owner, uint256 newStrength, uint256 level);
    event BattleResult(address indexed winner, address indexed loser);

    // Create a new hamster
    function createHamster(string memory _name) public {
        require(bytes(hamsterCollection[msg.sender].name).length == 0, "You already have a hamster!");
        
        hamsterCollection[msg.sender] = Hamster({
            name: _name,
            strength: 10,  // Initial strength of 10
            level: 1       // Starting at level 1
        });

        emit HamsterCreated(msg.sender, _name);
    }

    // Train your hamster to increase its strength
    function trainHamster() public {
        require(bytes(hamsterCollection[msg.sender].name).length > 0, "You must create a hamster first!");

        Hamster storage hamster = hamsterCollection[msg.sender];
        hamster.strength += 5;  // Increase strength by 5 each time
        hamster.level += 1;     // Level up each time hamster is trained

        emit HamsterTrained(msg.sender, hamster.strength, hamster.level);
    }

    // Battle between two hamsters
    function battle(address _opponent) public {
        require(bytes(hamsterCollection[msg.sender].name).length > 0, "You must create a hamster first!");
        require(bytes(hamsterCollection[_opponent].name).length > 0, "Opponent does not have a hamster!");

        Hamster storage myHamster = hamsterCollection[msg.sender];
        Hamster storage opponentHamster = hamsterCollection[_opponent];

        // Determine the winner by comparing strength
        if (myHamster.strength >= opponentHamster.strength) {
            wins[msg.sender] += 1;
            losses[_opponent] += 1;
            emit BattleResult(msg.sender, _opponent);
        } else {
            wins[_opponent] += 1;
            losses[msg.sender] += 1;
            emit BattleResult(_opponent, msg.sender);
        }
    }

    // View your hamster's stats
    function getMyHamster() public view returns (string memory name, uint256 strength, uint256 level) {
        Hamster memory hamster = hamsterCollection[msg.sender];
        require(bytes(hamster.name).length > 0, "You must create a hamster first!");
        return (hamster.name, hamster.strength, hamster.level);
    }

    // View wins and losses
    function getWinsAndLosses() public view returns (uint256 myWins, uint256 myLosses) {
        return (wins[msg.sender], losses[msg.sender]);
    }
}

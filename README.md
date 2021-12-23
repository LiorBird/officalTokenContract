

Changes in imports file to address the lock and unlock function loophole. 

New variable: 
bool private _renounced;

This will prevent the unlock function from working if ownership has been renounced: 
function renounceOwnership() public virtual onlyOwner {
        emit OwnershipTransferred(_owner, address(0));
        _owner = address(0);
        _renounced = true;
    }

Once renounced → renounced = true. Then unlocking is not permitted: 
    function unlock() public virtual {
        require(_previousOwner == msg.sender, "Only the previous owner can unlock onwership");
        require(_renounced == false, "Cannot unlock renounced ownership");
        require(block.timestamp > _lockTime , "The contract is still locked");
        emit OwnershipTransferred(_owner, _previousOwner);
        _owner = _previousOwner;
    }

Changes in Cync_final to adress the "out of gas" error: 
Added the following line “require(_excluded.length < 10…) to avoid out of gas error. 

    function excludeFromReward(address account) external onlyOwner() {
        require(!_isExcludedFromRewards[account], "Account is not included");
        require(_excluded.length < 10, "Cannot exclude more than 10 accounts");
        _exclude(account);
    }




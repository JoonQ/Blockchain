pragma solidity ^0.5.11;

// 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4    |   ETH ADDRESS OF CONTRACT OWNER   //
// 0xAb8483F64d9C6d1EcF9b849Ae677dD3315835cb2    |   ETH ADDRESS OF SELLER           //
// 0x03C6FcED478cBbC9a4FAB34eF9f40767739D1Ff7    |   ETH ADDRESS OF BUYER            //

contract joonRealEstateContract{
    
// DEFINING STATE VARIABLES AND TYPE //
    address owner;
    uint  propertyCount;
    
// INITIALIZES AND SETS THE PERSON/ETHEREUM ADDRESS WHO DEPLOY THE CONTRACT AS THE OWNER. SETS PROPERTY COUNT TO 1. //
    constructor() public{
        owner = msg.sender;
        propertyCount = 1;
    }
    
    
    struct property{
        uint propertyID;
        uint propertyValue;
        string propertyAddress;
        address ownerEthAddress;
        bool sold;
        //enum status { Pending, Sold }         /*CREATES STATUS - OF TYPE ENUM WITH TWO STATES NAMELY PENDING(0) OR SOLD(1).*/
    }    
    
    
    
// MAPS ADDRESS(KEY) TO STRUT PROPERTY(VALUES) AND NAMES EACH ENTRY SET AS MYPROPERTY[ADDRESS][N]. //
    mapping(address => property[]) public myProperty;
    
    
    
// MODIFIER NAMED ISOWNER,THIS CHECKS THAT SENDER OF CURRRENT MESSAGE (PERSON WHOM IS CURRENTLY CONNECTED TO THIS CONTRACT) IS OWNER // 
    modifier isOwner {
        require(msg.sender == owner);
        _; /* _ IS THE PLACE HOLDER FOR THE BELOW CODE TO EXECUTE IF THE CONDITION ABOVE (MSG.SENDER IS INDEED OWNER)*/
    }
    
    

/// SECTION A - ADD NEW PROPERTY ///
    function A_addProperty(uint _propertyValue, string memory _propertyAddress, address _ownerEthAddress) public isOwner{
        property memory newProperty = property
        ({
            propertyID: propertyCount,
            propertyValue: _propertyValue,
            propertyAddress: _propertyAddress,
            ownerEthAddress: _ownerEthAddress,
            sold: false /* NEW PROPERTY ADDED MARKED AS FALSE(NOT SOLD) */
        });
    myProperty[_ownerEthAddress].push(newProperty);
    propertyCount = propertyCount+1;
    }
    
    

/// SECTION B - BUY A PROPERTY (CHANGE OWNERSHIP) ///
    function B_buyProperty(uint _propertyID, address _newownerEthAddress) public returns (string memory){
        require(msg.sender != _newownerEthAddress);
        for(uint i=0; i<(myProperty[msg.sender].length); i++)
        {
            if(myProperty[msg.sender][i].propertyID == _propertyID)
            {
            property memory newOwner = property
            ({
                propertyID: _propertyID,
                propertyValue: myProperty[msg.sender][i].propertyValue,
                propertyAddress: myProperty[msg.sender][i].propertyAddress,
                ownerEthAddress: msg.sender,
                sold: true
            });
            delete myProperty[msg.sender][i];
            myProperty[_newownerEthAddress].push(newOwner);
            }
        }
    }



/// SECTION C - GET ALL LAND ID OF AN ADDRESS THAT ARE NOT SOLD ///
    uint [] notsold;
    function C_getUnsoldLandID(address _ownerEthAddress) public returns(uint[] memory)
    {
        for(uint i=0; i<notsold.length; i++)
        {
        delete notsold[i];
        }
        for (uint i=0; i<myProperty[_ownerEthAddress].length; i++)
        {
            if(myProperty[_ownerEthAddress][i].sold == false)
            {
                notsold.push(myProperty[_ownerEthAddress][i].propertyID);
            }
        }
        return notsold;
    }



/// SECTION D - GET PROPERTY DETAILS USING PROPERTY ID AND ADDRESS ///
    function D_getPropertyDetails(uint _propertyID, address _ownerEthAddress) view public returns(uint, uint, string memory, address, bool)
    {
        for(uint i=0; i<myProperty[_ownerEthAddress].length; i++)
        {
            if(myProperty[_ownerEthAddress][i].propertyID == _propertyID)
            {
                return
                (
               myProperty[_ownerEthAddress][i].propertyID,
               myProperty[_ownerEthAddress][i].propertyValue,
               myProperty[_ownerEthAddress][i].propertyAddress,
               myProperty[_ownerEthAddress][i].ownerEthAddress,
               myProperty[_ownerEthAddress][i].sold
                );
            }
        }
    }



/// SECTION E - EDIT PROPERTY DETAILS ///
    function E_editPropertyDetails(uint _propertyID, uint _editPropertyValue, string memory _editPropertyAddress, address _editOwnerEthAddress, bool _editStatus) public
    {
        address propertyEthAddress = msg.sender;
        
        for (uint i=0; i<myProperty[propertyEthAddress].length; i++)
        {
            if (myProperty[propertyEthAddress][i].propertyID == _propertyID)
            {
                myProperty[propertyEthAddress][i].propertyValue = _editPropertyValue;
                myProperty[propertyEthAddress][i].propertyAddress = _editPropertyAddress;
                myProperty[propertyEthAddress][i].ownerEthAddress = _editOwnerEthAddress;
                myProperty[propertyEthAddress][i].sold = _editStatus;
            }
        }
    }
    
    
}

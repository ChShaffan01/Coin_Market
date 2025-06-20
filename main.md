// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract market{

struct Details{
 address owner;
 string tokenName;
 string Symbol;
 uint quantity;
 uint LeftToken;
 uint tokenPrice;
  }
 struct Holder{
    address Owner;
    uint TotalQuantity;
    uint TotalPrice;
    uint[] ids;
 }

uint Tokenid;
uint public CoinRegistrationFee = 0.0009 ether;
mapping(uint => Details) public Market;
mapping(uint => Holder) public holders;

event Register(uint indexed  id , address owner);
event Buyer(uint indexed  id , address buyer , uint quantity);

function checkTokenDetails(uint _id) view external returns (Details memory){
    return Market[_id];
}
function LonchTeken(string memory _tokenName, string memory _Symbol, uint  _quantity , uint _PertokenPrice) external payable {
    require(_quantity > 0 ,"quantity greather then zero");
    require(msg.value >= CoinRegistrationFee , "Must pay transaction fee");
    Details storage Lister = Market[Tokenid];
    Lister.owner = msg.sender;
    Lister.tokenName = _tokenName;
    Lister.Symbol = _Symbol;
    Lister.quantity = _quantity;
    Lister.LeftToken = _quantity;
    Lister.tokenPrice = _PertokenPrice;
   
 emit Register(Tokenid , msg.sender);
 Tokenid++;
}

function BuyAToken(uint id , uint _quantity) external payable {
    Details storage TokenOwner = Market[id]; 
    require(_quantity > 0 , "Not zero");
    require(TokenOwner.LeftToken > _quantity , "Not avalible");
    uint amount = TokenOwner.tokenPrice *_quantity;
    require(msg.value >= amount , "insufficient balance");
    if (msg.value > amount){
        payable (msg.sender).transfer( msg.value - amount);
    }
    payable (TokenOwner.owner).transfer(amount);
    TokenOwner.LeftToken - _quantity;

    Holder storage buyer = holders[id];
    buyer.ids.push(id);
    buyer.Owner = msg.sender;
    buyer.TotalPrice = amount;
    buyer.TotalQuantity = _quantity;

    emit Buyer(id, msg.sender , _quantity);
}

function CheckYourToken(uint id) view external returns(Holder memory){
    require(msg.sender ==  holders[id].Owner , "only owner");
    return holders[id];
}
}

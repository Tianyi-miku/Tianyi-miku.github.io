---
title: solidity抽奖合约
date: 2022-07-16 19:21:20
tags:
    - solidity
    - web3
    - Hardhat
categories: [学习,solidity]
---
## 随机数

链上没法生成真正的随机数,真正的随机数要用到预言机
<!-- more -->
根据keccak256 伪随机数
``` 
         /*
         * 例上随机只依赖区块哈希，调用者地址，和区块时间，
         * 想提高随机性可以再增加一些属性比如nonce等，但是不能根本上解决安全问题
         */
    function getRound() external view returns (uint256) {
        bytes32 randomBytes = keccak256(
            abi.encodePacked(
                blockhash(block.number - 1),
                msg.sender,
                block.timestamp
            )
        );
        return uint256(randomBytes);
    }
```
chainlink 预言机

```
contract RandomNumberlottery is VRFV2WrapperConsumerBase, ConfirmedOwner {
    event RequestSent(uint256 requestId, uint32 numWords, uint256 paid);
    event RequestFulfilled(
        uint256 requestId,
        uint256[] randomWords,
        uint256 payment
    );
    event Log(address, uint256, uint);
    error InsufficientFunds(uint256 balance, uint256 paid);
    error RequestNotFound(uint256 requestId);
    error LinkTransferError(address sender, address receiver, uint256 amount);
    error JoinPaly_Error(uint);
    error slottery_Error(uint);
    error CallFailed();

    struct RequestStatus {
        uint256 paid; // amount paid in link
        bool fulfilled; // whether the request has been successfully fulfilled
        uint256[] randomWords;
    }
    mapping(uint256 => RequestStatus)
        public s_requests; /* requestId --> requestStatus */

    // past requests Id.
    uint256[] public requestIds;
    uint256 public lastRequestId;

    uint256 totality; //总数
    mapping(address => uint256) public peopleTomoney; //地址到钱的映射
    address[] people;
    address winer;
    uint32 _callbackGasLimit = 300000;
    uint16 _requestConfirmations = 3;
    uint32 _numWords = 3;

    // configuration: https://docs.chain.link/vrf/v2/direct-funding/supported-networks#configurations
    constructor(
        address _linkAddress,
        address _wrapperAddress
    )
        ConfirmedOwner(msg.sender)
        VRFV2WrapperConsumerBase(_linkAddress, _wrapperAddress)
    {}

    //参与抽奖每次0.1
    function joinPlay() public payable {
        if (msg.value != 0.1 ether) {
            revert JoinPaly_Error(msg.value);
        }
        peopleTomoney[msg.sender] = msg.value;
        people.push(msg.sender);
        console.log(people.length);
        emit Log(msg.sender, msg.value, block.timestamp);
    }

    function getwiner() public view returns (address) {
        return winer;
    }

    // Depends on the number of requested values that you want sent to the
    // fulfillRandomWords() function. Test and adjust
    // this limit based on the network that you select, the size of the request,
    // and the processing of the callback request in the fulfillRandomWords()
    // function.
    // The default is 3, but you can set this higher.
    // For this example, retrieve 2 random values in one request.
    // Cannot exceed VRFV2Wrapper.getConfig().maxNumWords.
    function requestRandomWords() public onlyOwner returns (uint256 requestId) {
        if (address(this).balance < 0.1 ether) {
            revert slottery_Error(address(this).balance);
        }
        requestId = requestRandomness(
            _callbackGasLimit,
            _requestConfirmations,
            _numWords
        );
        uint256 paid = VRF_V2_WRAPPER.calculateRequestPrice(_callbackGasLimit);
        uint256 balance = LINK.balanceOf(address(this));
        if (balance < paid) revert InsufficientFunds(balance, paid);
        s_requests[requestId] = RequestStatus({
            paid: paid,
            randomWords: new uint256[](0),
            fulfilled: false
        });
        requestIds.push(requestId);
        lastRequestId = requestId;
        emit RequestSent(requestId, _numWords, paid);
        return requestId;
    }

    function fulfillRandomWords(
        uint256 _requestId,
        uint256[] memory _randomWords
    ) internal override {
        RequestStatus storage request = s_requests[_requestId];
        if (request.paid == 0) revert RequestNotFound(_requestId);
        request.fulfilled = true;
        request.randomWords = _randomWords;
        //这里处理winer 简单取模people长度，默认第一个
        uint256 index = _randomWords[0] % people.length;
        winer = people[index];
        console.log(winer);
        emit RequestFulfilled(_requestId, _randomWords, request.paid);
    }

    function getNumberOfRequests() external view returns (uint256) {
        return requestIds.length;
    }

    function getRequestStatus(
        uint256 _requestId
    )
        external
        view
        returns (uint256 paid, bool fulfilled, uint256[] memory randomWords)
    {
        RequestStatus memory request = s_requests[_requestId];
        if (request.paid == 0) revert RequestNotFound(_requestId);
        return (request.paid, request.fulfilled, request.randomWords);
    }

    /**
     * Allow withdraw of Link tokens from the contract
     */
    function withdrawLink(address _receiver) public onlyOwner {
        bool success = LINK.transfer(_receiver, LINK.balanceOf(address(this)));
        if (!success)
            revert LinkTransferError(
                msg.sender,
                _receiver,
                LINK.balanceOf(address(this))
            );
    }

    function winerdraw() public {
        if (msg.sender == winer) {
            (bool success, ) = winer.call{value: address(this).balance}("");
            if (!success) {
                revert CallFailed();
            }
            delete people;
            delete winer;
        }
    }

    fallback() external payable {
        console.log("fallback");
    }

    receive() external payable {
        console.log("receive");
    }
}
```
# Lottery-forstudy

一个基于 Foundry 构建的去中心化彩票应用，利用 Chainlink VRF 实现可验证的随机性，遵循智能合约最佳实践，确保抽奖过程的公平性、透明度和安全性。

## 项目简介

本项目旨在提供一个无需信任的区块链彩票解决方案，运行于以太坊兼容网络。核心优势在于：
- 随机数生成不可篡改：通过 Chainlink VRF 实现可验证的随机结果，杜绝人为操纵
- 全程透明可追溯：所有参与记录、开奖结果均上链，公开可查
- 自动化流程：无需人工干预，从参与、开奖到奖金发放全流程智能合约自动执行
- 低门槛参与：支持任意符合条件的用户支付指定费用参与

## 技术栈

| 工具/框架       | 用途                          |
|-----------------|-------------------------------|
| Foundry         | 智能合约开发、测试、部署框架  |
| Chainlink VRF   | 提供可验证随机数生成服务      |
| Solidity        | 智能合约开发语言              |
| Ethereum 兼容链 | 合约部署与运行环境（支持测试网/主网） |
| Makefile        | 简化开发、部署命令流程        |

Foundry 官方文档：[https://book.getfoundry.sh/](https://book.getfoundry.sh/)  
Chainlink VRF 文档：[https://docs.chain.link/vrf](https://docs.chain.link/vrf)

## 前提条件

在使用本项目前，请确保已完成以下准备：
1. 安装 Foundry 开发框架：
   ```shell
   curl -L https://foundry.paradigm.xyz | bash
   foundryup
   ```
2. 配置.env文件
```.env
SEPLOLIA_RPC_URL = your rpc url
ETHERSCAN_API_KEY=your-etherscan-api-key
```
3. 确保部署账户拥有足够的测试网 ETH（支付 Gas 费用）和 LINK 代币（支付 VRF 服务费用）

## 快速开始
1. 克隆项目
 ```shell
 git clone https://github.com/MiracleCS/Lottery-forstudy.git
cd Lottery-forstudy
```
2. 安装依赖
```shell
make install
```
依赖库清单：
cyfrin/foundry-devops@0.2.2（开发运维工具）
smartcontractkit/chainlink-brownie-contracts@1.1.1（Chainlink 合约库）
foundry-rs/forge-std@v1.8.2（Foundry 标准测试库）
transmissions11/solmate@v6（轻量级合约组件库）

3. 编译合约
```shell
forge build
```
4. 运行测试
```shell
# 运行所有测试用例
forge test

# 运行指定测试（示例）
forge test --match-test testRaffleEnterWithCorrectFee

# 运行测试并显示 Gas 消耗
forge test -vvv --gas-report
```
5. 部署合约
```shell
# 部署到 Sepolia 测试网
make deploy-sepolia
```
## 核心功能说明
1. 彩票参与流程
   - 用户支付指定费用参与彩票
   - 合约验证支付金额与彩票状态（需为「开放」状态）
   - 记录用户地址到参与者列表
   - 记录用户地址到参与者列表
2. 开奖机制
  - 开奖条件：达到指定时间间隔 AND 参与者数量 ≥ 1
  - 随机数生成：合约自动向 Chainlink VRF 发送随机数请求
  - 获奖者选择：通过 VRF 返回的随机数取模参与者数量，确定获奖者
  - 奖金发放：合约自动将奖池资金（所有入场费总和）转账给获奖者
3. Chainlink VRF 配置流程
   - 部署合约前，需在 Chainlink 控制台 创建 VRF 订阅
   - 部署合约后，将合约地址添加为 VRF 订阅的「消费者」
   - 向订阅账户充值 LINK 代币（默认 3 LINK 可支持多次随机数请求）
4. 合约状态管理

| 状态       | 描述                          |
|-----------------|-------------------------------|
| OPEN（开放）	        | 智彩票可正常参与  |
| CALCULATING（计算中）   | 等待 VRF 随机数返回      |

## 注意事项
1. 安全提醒：
请勿在主网使用未经审计的合约版本
测试网部署时，请勿使用包含真实资产的钱包私钥
.env 文件包含敏感信息，已加入 .gitignore，切勿提交到代码库
2. 部署须知：
不同网络的 Chainlink VRF 参数不同，需在 HelperConfig.s.sol 中配置
确保 VRF 订阅已添加合约为消费者，且有足够 LINK 余额
入场费、开奖间隔等参数可在部署脚本中调整
3. 本地测试：
本地测试时，Anvil 会自动部署 VRF 协调器模拟合约和 LINK 测试代币
无需手动配置 Chainlink 订阅，测试脚本会自动处理
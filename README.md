TokenVault: Production-Grade UUPS Upgradeable ProtocolA robust, secure smart contract system implementing a three-stage upgrade lifecycle using the Universal Upgradeable Proxy Standard (UUPS). This protocol manages user assets, implements yield-bearing logic, and enforces governance-controlled security delays.
📌 Overview
The TokenVault protocol is designed for long-term evolution. By utilizing the UUPS pattern (EIP-1822), the upgrade logic resides within the implementation contract, allowing for significant gas savings and a cleaner separation of concerns compared to Transparent Proxies.Protocol VersionsV1 (Foundational): Core deposit/withdrawal functionality with a configurable fee structure.V2 (Yield & Control): Introduction of annual yield calculations and emergency pause mechanisms.V3 (Safety & Governance): Implementation of two-step withdrawal delays and an emergency exit bypass for extreme scenarios.
🏗 Project StructurePlaintextyour-repo/
├── contracts/
│   ├── TokenVaultV1.sol      # Initial deployment: Deposits/Withdrawals
│   ├── TokenVaultV2.sol      # Upgrade 1: Yield & Pausability
│   ├── TokenVaultV3.sol      # Upgrade 2: Withdrawal Delays & Emergency Exit
│   └── mocks/
│       └── MockERC20.sol     # ERC20 Token for testing
├── test/
│   ├── TokenVaultV1.test.js  # Functional testing for V1
│   ├── upgrade-v1-to-v2.test.js # State migration and V2 logic validation
│   ├── upgrade-v2-to-v3.test.js # State migration and V3 logic validation
│   └── security.test.js      # UUPS, RBAC, and Storage collision tests
├── scripts/
│   ├── deploy-v1.js          # Deploys Proxy + V1 Implementation
│   ├── upgrade-to-v2.js      # Upgrades Proxy to V2 Implementation
│   └── upgrade-to-v3.js      # Upgrades Proxy to V3 Implementation
├── hardhat.config.js         # Hardhat configuration & compiler settings
├── package.json              # Project dependencies
└── README.md                 # System documentation
🔒 Security Architecture1. Storage Layout ManagementTo prevent storage collisions during upgrades, we utilize a strict storage gap strategy. Every implementation contract maintains a uint256[50] slot space, which is reduced as new state variables are appended.Variable VersionNew Variables AddedRemaining Gap SlotsV1token, depositFee, totalDeposits, balances44 SlotsV2yieldRate, lastYieldClaim, depositsPaused41 SlotsV3withdrawalDelay, withdrawalRequests39 Slots2. Initialization SecurityDisabled Constructors: All implementation contracts call _disableInitializers() in the constructor to prevent unauthorized initialization of the logic contract.Initializer Modifiers: Uses OpenZeppelin’s initializer (V1) and reinitializer (V2/V3) to ensure setup code only runs once per version.3. Access Control (RBAC)The system employs a multi-role hierarchy:DEFAULT_ADMIN_ROLE: Critical administrative management.UPGRADER_ROLE: Authorized to execute UUPS upgrades via _authorizeUpgrade.PAUSER_ROLE: Authorized to pause deposits in case of emergency.
📈 Yield Logic (V2)Yield is calculated based on a per-user basis to ensure fairness and prevent compounding manipulation:$$Yield = \frac{userBalance \times yieldRate \times timeElapsed}{365 days \times 10000}$$yieldRate: Measured in basis points (500 = 5%).Non-Compounding: Yield is tracked separately and does not auto-compound into the principal balance.⏱ Withdrawal Delay (V3)To mitigate flash loan attacks or sudden liquidity drains, V3 introduces a two-step withdrawal:Request: User initiates requestWithdrawal(amount).Wait: Must wait for the duration specified in withdrawalDelay.Execute: Call executeWithdrawal() to receive funds.
🚀 Development & DeploymentSetupBashnpm install
CompilationBashnpx hardhat compile
Running TestsThe test suite validates functional requirements, security invariants, and cross-version state persistence.Bashnpx hardhat test
Deployment (Local/Testnet)Bashnpx hardhat run scripts/deploy-v1.js --network <network-name>
📋 Compliance Checklist[x] Zero usage of selfdestruct or delegatecall in implementation.[x] Storage gaps properly accounted for in V2/V3.[x] All implementation contracts prevent direct initialization.[x] Upgradeability authorized only by UPGRADER_ROLE.

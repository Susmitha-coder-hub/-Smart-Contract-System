TokenVault UUPS Upgradeable ProtocolA production-grade, secure, and 
upgradeable smart contract system using the Universal Upgradeable Proxy Standard (UUPS).
This protocol demonstrates state preservation, storage layout management, and access control across three major versions.
🏗 System ArchitectureThe system utilizes the UUPS pattern (EIP-1822), where the upgrade logic resides within the implementation contract.
This provides higher gas efficiency and better security compared to Transparent Proxy patterns.Version RoadmapV1 (Foundational): Basic deposit/withdraw with fee logic and Role-Based Access Control (RBAC).V2 (Yield): Adds yield generation mechanisms, last-claim tracking, and emergency pause functionality.V3 (Governance & Safety): Implements withdrawal delays (2-step withdrawal) and an emergency exit bypass.🛠 Project StructurePlaintextyour-repo/
├── contracts/
│   ├── TokenVaultV1.sol      # Core deposit/withdraw logic
│   ├── TokenVaultV2.sol      # Yield and Pausing
│   ├── TokenVaultV3.sol      # Withdrawal delays and Emergency mechanisms
│   └── mocks/
│       └── MockERC20.sol     # Standard ERC20 for testing
├── test/
│   ├── TokenVaultV1.test.js  # V1 Functional tests
│   ├── upgrade-v1-to-v2.test.js
│   ├── upgrade-v2-to-v3.test.js
│   └── security.test.js      # UUPS & Access control security
├── scripts/
│   ├── deploy-v1.js
│   ├── upgrade-to-v2.js
│   └── upgrade-to-v3.js
├── hardhat.config.js
└── package.json
🔐 Security & Storage ManagementStorage LayoutTo prevent storage collisions,
we implement a strict Storage Gap strategy. Each version maintains a consistent variable order and reduces the gap size as new state variables are introduced.
VersionState AddedGap AdjustmentV1token, depositFee, totalDeposits, balancesuint256[44] private __gap;V2yieldRate, lastYieldClaim,
depositsPauseduint256[41] private __gap;V3withdrawalDelay,
withdrawalRequestsuint256[39] private __gap;Initializer
SecurityConstructor Protection: All implementation contracts 
use _disableInitializers() in the constructor to prevent the logic contract from being taken over.Access Control: Upgrades are restricted to addresses with the UPGRADER_ROLE.Reinitialization: V2 and V3 use the reinitializer(version) modifier to safely set new state variables during the upgrade process.
🚀 Getting StartedPrerequisitesNode.js v16+HardhatInstallationBashnpm install
CompilationBashnpx hardhat compile
Running TestsThe test suite covers functional logic, state migration, and security vulnerabilities.Bash# Run all tests
npx hardhat test

# Run specific security tests
npx hardhat test test/security.test.js
🧪 Key Test CasesThe system is validated against the following critical scenarios:State Persistence: Ensuring totalDeposits and user balances remain identical after moving from V1 to V2.Yield Calculation: Yield = (balance * rate * time) / (365 days * 10000).Withdrawal Delay: Enforcing that executeWithdrawal() can only be called after withdrawalDelay has passed since requestWithdrawal().Unauthorized Upgrades: Ensuring accounts without UPGRADER_ROLE cannot point the proxy to a new implementation.📄 LicenseThis project is licensed under the MIT License.

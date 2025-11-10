# KipuBankV3

KipuBankV3 es un contrato inteligente de bóveda personal multimoneda con control de acceso, límites de retiro y un tope global de depósitos en USD, utilizando oráculos de Chainlink y swaps automáticos vía Uniswap V2.
 Características
-  Depósitos y retiros de ETH y tokens ERC-20
-  Control de acceso con roles (DEFAULT_ADMIN_ROLE, MANAGER_ROLE)
-  Límite global de depósitos en USD (con Chainlink ETH/USD)
-  Límite de retiro por transacción
-  Conversión automática de tokens a USDC vía Uniswap V2
-  Protección contra reentradas con mutex simple
-  Cacheo de decimales para ahorro de gas
-  Contadores de depósitos y retiros por usuario y token

    Arquitectura
- ETH: Se gestiona directamente con vaults[msg.sender]
- Tokens ERC-20: Se almacenan en vaultsByToken[msg.sender][token]
- USDC: Token base para conversiones y control de cap
- Chainlink: Oráculo ETH/USD para validar el cap global
- Uniswap V2: Para swap de tokens no-USDC a USDC

   Despliegue
constructor(
    uint256 _withdrawalLimit,       // Límite de retiro por transacción (en wei)
    uint256 _bankCapUSD,            // Cap global en USD (6 decimales)
    address _uniswapRouter,         // Dirección del router de Uniswap V2 0x2D99ABD9008Dc933ff5c0CD271B88309593aB921
    address _uniswapFactory         // Dirección de la factory de Uniswap V2 0x5C69bEe701ef814a2B6a3EDD4B1652CB9cc5aA6f
) payable

Si se envía ETH al constructor, se deposita automáticamente en la bóveda del deployer.

 Funciones principales
Depósitos
- deposit(): Deposita ETH
- depositToken(address token, uint256 amount): Deposita tokens ERC-20
- depositAnyToken(address token, uint256 amount): Convierte a USDC si es necesario y deposita
Retiros
- withdraw(uint256 amount): Retira ETH
- withdrawToken(address token, uint256 amount): Retira tokens ERC-20
Vistas
- getMyVaultBalance(): ETH del usuario
- getVaultBalanceOf(address user): ETH de otro usuario
- getVaultBalanceOfToken(address user, address token): Token balance
- convertETHtoUSD(uint256 wei): Conversión a USD
- convertTokenToUSD(address token, uint256 amount): Conversión a USD
 Seguridad
-  Reentrancy guard (nonReentrant)
-  Validaciones de cap (validDepositCapUSD, validTokenDepositCapUSD)
-  Validaciones de balance y límites
-  Errores personalizados (BankCapExceeded, Reentrancy, UnsupportedDecimals, etc.)

 Red de pruebas
- Red: Sepolia
- USDC: 0x7Fc21ceb0C5003576ab5E101eB240c2b822c95d2
- Chainlink ETH/USD: 0x694AA1769357215DE4FAC081bf1f309aDC325306


<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle de Gastos Domésticos</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6;
        }
        .main-container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 2rem;
        }
        .card {
            background-color: #ffffff;
            border-radius: 1rem;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            padding: 1.5rem;
        }
        .summary-card {
            background-color: #e2e8f0;
            border-radius: 1rem;
            padding: 1rem;
        }
        .category-item {
            display: flex;
            align-items: center;
            padding: 0.5rem 0;
        }
        .category-color-box {
            width: 1.5rem;
            height: 1.5rem;
            border-radius: 0.5rem;
            margin-right: 0.75rem;
        }
        input, select, textarea {
            border-radius: 0.5rem;
            border: 1px solid #d1d5db;
        }
        .text-income {
            color: #10b981;
        }
        .text-expense {
            color: #ef4444;
        }
        .text-overdue {
            color: #ef4444;
            font-weight: 700;
        }
        .text-paid {
            color: #10b981;
            font-weight: 700;
        }
        .spinner {
            border: 4px solid rgba(255, 255, 255, 0.3);
            border-top: 4px solid #3b82f6;
            border-radius: 50%;
            width: 24px;
            height: 24px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        #categoryChart {
            max-height: 250px;
        }
        .tab-button {
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 0.75rem 1.5rem;
            font-weight: 600;
            transition: all 0.2s ease-in-out;
            cursor: pointer;
            border-bottom: 3px solid transparent;
        }
        .tab-button.active {
            border-bottom: 3px solid #3b82f6;
            color: #3b82f6;
        }
        .tab-content {
            display: none;
        }
        .tab-content.active {
            display: block;
        }
        
    </style>
</head>
<body class="bg-gray-100 text-gray-800">

<div id="app" class="main-container">
    <div class="flex justify-center bg-white rounded-xl shadow-md p-4 mb-8 space-x-4">
        <button id="tab-lancamentos" class="tab-button active">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 13h6m-3-3v6m-9 1a9 9 0 1118 0 9 9 0 01-18 0z" />
            </svg>
            Lançamentos
        </button>
        <button id="tab-compras" class="tab-button">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.185 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z" />
            </svg>
            Compras
        </button>
        <button id="tab-resumos" class="tab-button">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 3.055A9.001 9.001 0 1020.945 13H11V3.055z" />
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.488 9H15V3.512A9.025 9.025 0 0120.488 9z" />
            </svg>
            Resumos
        </button>
        <button id="tab-configuracoes" class="tab-button">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" />
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
            </svg>
            Configurações
        </button>
    </div>

    <div id="lancamentos-tab" class="tab-content active space-y-8">
        <div class="card">
            <h2 class="text-2xl font-bold mb-4">Adicionar Transação</h2>
            <form id="transaction-form" class="space-y-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700">Descrição</label>
                    <input type="text" id="description" placeholder="Ex: Conta de luz" required class="mt-1 block w-full px-3 py-2">
                </div>
                <!-- Nova linha com duas colunas para Valor e Data -->
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Valor</label>
                        <input type="number" id="amount" placeholder="0.00" step="0.01" required class="mt-1 block w-full px-3 py-2">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Data da Transação</label>
                        <input type="date" id="transaction-date" required class="mt-1 block w-full px-3 py-2">
                    </div>
                </div>
                <!-- Nova linha com três colunas para Tipo, Pagador e Categoria -->
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Tipo</label>
                        <select id="type" required class="mt-1 block w-full px-3 py-2">
                            <option value="despesa">Despesa</option>
                            <option value="receita">Receita</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Pagador</label>
                        <select id="payer" required class="mt-1 block w-full px-3 py-2">
                            <option value="Lais">Lais</option>
                            <option value="André">André</option>
                            <option value="Lais/André">Lais/André</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Categoria</label>
                        <select id="category-select" class="mt-1 block w-full px-3 py-2" required>
                            <option value="" disabled selected>Selecione uma categoria</option>
                        </select>
                    </div>
                    <div id="subcategory-container" class="hidden">
                        <label class="block text-sm font-medium text-gray-700">Subcategoria</label>
                        <select id="subcategory-select" class="mt-1 block w-full px-3 py-2" required></select>
                    </div>
                </div>
                <!-- Nova linha para Forma de Pagamento -->
                <div id="payment-method-container">
                    <label class="block text-sm font-medium text-gray-700">Forma de Pagamento</label>
                    <select id="payment-method" class="mt-1 block w-full px-3 py-2" required>
                        <option value="" disabled selected>Selecione a forma de pagamento</option>
                        <option value="Cartão de Crédito">Cartão de Crédito</option>
                        <option value="Cartão de Crédito - Black">Cartão de Crédito - Black</option>
                        <option value="Cartão de Débito">Cartão de Débito</option>
                        <option value="Cartão de Débito - Green">Cartão de Débito - Green</option>
                        <option value="Cartão de Crédito - Flash">Cartão de Crédito - Flash</option>
                        <option value="Dinheiro">Dinheiro</option>
                        <option value="PIX">PIX</option>
                    </select>
                </div>
                <div class="flex items-center space-x-4">
                    <div class="flex items-center space-x-2">
                        <input type="checkbox" id="is-fixed" class="rounded text-blue-500">
                        <label for="is-fixed" class="text-sm font-medium text-gray-700">Despesa Fixa?</label>
                    </div>
                    <div class="flex items-center space-x-2">
                        <input type="checkbox" id="is-paid" class="rounded text-green-500">
                        <label for="is-paid" class="text-sm font-medium text-gray-700">Pago?</label>
                    </div>
                </div>
                <!-- Nova seção para despesas recorrentes e parceladas -->
                <div class="flex items-center space-x-2">
                    <input type="checkbox" id="is-recurring" class="rounded text-blue-500">
                    <label for="is-recurring" class="text-sm font-medium text-gray-700">Despesa Recorrente?</label>
                </div>
                <div id="recurring-months-group" class="hidden">
                    <label class="block text-sm font-medium text-gray-700">Repetir por quantos meses?</label>
                    <input type="number" id="recurring-months" min="1" value="1" class="mt-1 block w-full px-3 py-2">
                </div>
                <div class="flex items-center space-x-2">
                    <input type="checkbox" id="is-installment" class="rounded text-blue-500">
                    <label for="is-installment" class="text-sm font-medium text-gray-700">Despesa Parcelada?</label>
                </div>
                <div id="installments-group" class="hidden">
                    <label class="block text-sm font-medium text-gray-700">Número de Parcelas</label>
                    <input type="number" id="installments-number" min="1" value="1" class="mt-1 block w-full px-3 py-2">
                </div>
                <!-- Fim da nova seção -->
                <button type="submit" class="w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-xl transition duration-300">
                    Adicionar
                </button>
            </form>
        </div>

        <!-- Dinheiro Guardado -->
        <div class="card">
            <h2 class="text-2xl font-bold mb-4">Dinheiro Guardado</h2>
            <div class="summary-card mb-4">
                <div class="flex justify-between font-bold text-lg">
                    <span>Saldo Total:</span>
                    <span id="savings-balance">R$ 0,00</span>
                </div>
            </div>
            <form id="savings-form" class="space-y-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700">Valor</label>
                    <input type="number" id="savings-amount" placeholder="0.00" step="0.01" required class="mt-1 block w-full px-3 py-2">
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700">Pagador</label>
                    <select id="savings-payer" required class="mt-1 block w-full px-3 py-2">
                        <option value="Lais">Lais</option>
                        <option value="André">André</option>
                        <option value="Lais/André">Lais/André</option>
                    </select>
                </div>
                <div class="flex justify-between space-x-4">
                    <button type="button" id="add-to-savings" class="w-1/2 bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-xl transition duration-300">
                        Guardar
                    </button>
                    <button type="button" id="withdraw-from-savings" class="w-1/2 bg-gray-500 hover:bg-gray-600 text-white font-bold py-2 px-4 rounded-xl transition duration-300">
                        Retirar
                    </button>
                </div>
            </form>
            
            <div class="summary-card mt-4">
                <h4 class="font-bold mb-2">Adicionar Rendimento</h4>
                <div class="space-y-2">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Valor do Rendimento</label>
                        <input type="number" id="income-amount" placeholder="0.00" step="0.01" required class="mt-1 block w-full px-3 py-2">
                    </div>
                    <button type="button" id="add-income-to-savings" class="w-full bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-xl transition duration-300">
                        Adicionar
                    </button>
                </div>
            </div>

            <div class="summary-card mt-4">
                <h4 class="font-bold mb-2">Extrato da Caixinha</h4>
                <div id="savings-transactions-list" class="space-y-2"></div>
            </div>
        </div>

        <!-- Receitas Específicas -->
        <div class="card">
            <h2 class="text-2xl font-bold mb-4">Receitas</h2>
            <div class="summary-card mt-4">
                <h4 class="font-bold mb-2">Saldos por Tipo de Receita</h4>
                <div id="specific-income-summary" class="space-y-2"></div>
            </div>
        </div>
    </div>
    
    <div id="compras-tab" class="tab-content space-y-8">
        <!-- Lista de Compras -->
        <div class="card">
            <h2 class="text-2xl font-bold mb-4">Lista de Compras</h2>
            <form id="shopping-list-form" class="space-y-4">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Item</label>
                        <input type="text" id="shopping-item" placeholder="Ex: Arroz 5kg" required class="mt-1 block w-full px-3 py-2">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Valor Esperado</label>
                        <input type="number" id="shopping-value" placeholder="0.00" step="0.01" class="mt-1 block w-full px-3 py-2">
                    </div>
                </div>
                 <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Quantidade</label>
                        <input type="number" id="shopping-quantity" placeholder="1" min="1" value="1" class="mt-1 block w-full px-3 py-2">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Unidade</label>
                        <select id="shopping-unit" class="mt-1 block w-full px-3 py-2">
                            <option value="unidade">unidade</option>
                            <option value="kg">kg</option>
                            <option value="litro">litro</option>
                            <option value="pacote">pacote</option>
                        </select>
                    </div>
                </div>
                <button type="submit" class="w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-xl transition duration-300">
                    Adicionar à Lista
                </button>
            </form>

            <div id="shopping-list-container" class="mt-6">
                <div class="flex justify-between items-center mb-2">
                    <h3 class="text-xl font-bold">Itens para Comprar</h3>
                    <button id="generate-transaction-btn" class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-xl transition duration-300 text-sm">
                        Gerar Lançamento
                    </button>
                </div>
                <div id="shopping-list-items" class="space-y-2"></div>
                <div id="shopping-total-bought" class="mt-4 font-bold text-lg flex justify-between">
                    <span>Total de Itens Comprados:</span>
                    <span>R$ 0,00</span>
                </div>
            </div>
        </div>
    </div>
    
    <div id="resumos-tab" class="tab-content space-y-8">
        <!-- Resumo Mensal -->
        <div class="card">
            <h2 class="text-2xl font-bold mb-4">Resumo Mensal</h2>
            <div class="flex justify-between items-center mb-4">
                <button id="prev-month" class="bg-gray-200 hover:bg-gray-300 text-gray-800 font-bold py-2 px-4 rounded-xl">Mês Anterior</button>
                <h3 id="current-month" class="text-lg font-semibold"></h3>
                <button id="next-month" class="bg-gray-200 hover:bg-gray-300 text-gray-800 font-bold py-2 px-4 rounded-xl">Próximo Mês</button>
            </div>
            
            <div id="monthly-summary" class="space-y-4">
                <div class="flex justify-between font-bold text-lg">
                    <span>Receita Total:</span>
                    <span id="total-income" class="text-income">R$ 0,00</span>
                </div>
                <div class="flex justify-between font-bold text-lg">
                    <span>Despesa Total:</span>
                    <span id="total-expense" class="text-expense">R$ 0,00</span>
                </div>
                <div class="summary-card mt-4">
                    <h4 class="font-bold mb-2">Despesas por Categoria (Gráfico)</h4>
                    <canvas id="categoryChart"></canvas>
                </div>
                <div class="summary-card mt-4">
                    <h4 class="font-bold mb-2">Despesas por Categoria (Tabela)</h4>
                    <div id="monthly-category-summary" class="space-y-2"></div>
                </div>
                <div class="summary-card mt-4">
                    <h4 class="font-bold mb-2">Despesas por Pagador</h4>
                    <div id="payer-summary" class="space-y-2"></div>
                </div>
                <div class="summary-card mt-4">
                    <h4 class="font-bold mb-2">Despesas por Forma de Pagamento</h4>
                    <div id="payment-summary" class="space-y-2"></div>
                </div>
            </div>

            <div class="mt-6">
                <h3 class="text-xl font-bold mb-2">Transações</h3>
                <div class="mb-4 space-y-4">
                    <label class="block text-sm font-medium text-gray-700">Filtrar por:</label>
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                        <select id="filter-payer" class="mt-1 block w-full px-3 py-2 rounded-xl">
                            <option value="all">Todos Pagadores</option>
                            <option value="Lais">Lais</option>
                            <option value="André">André</option>
                            <option value="Lais/André">Lais/André</option>
                        </select>
                        <select id="filter-category" class="mt-1 block w-full px-3 py-2 rounded-xl">
                            <option value="all">Todas as Categorias</option>
                        </select>
                        <select id="filter-subcategory" class="mt-1 block w-full px-3 py-2 rounded-xl" disabled>
                            <option value="all">Todas as Subcategorias</option>
                        </select>
                    </div>
                </div>
                <div id="transaction-list" class="space-y-2"></div>
            </div>
            <!-- Botão para copiar o resumo -->
            <button id="copy-summary-btn" class="w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-xl transition duration-300">
                Copiar Resumo Mensal
            </button>
        </div>

        <!-- Resumo Anual -->
        <div class="card">
            <h2 class="text-2xl font-bold mb-4">Resumo Anual</h2>
            <div class="flex justify-between items-center mb-4">
                <button id="prev-year" class="bg-gray-200 hover:bg-gray-300 text-gray-800 font-bold py-2 px-4 rounded-xl">Ano Anterior</button>
                <h3 id="current-year" class="text-lg font-semibold"></h3>
                <button id="next-year" class="bg-gray-200 hover:bg-gray-300 text-gray-800 font-bold py-2 px-4 rounded-xl">Próximo Ano</button>
            </div>
            <div id="annual-summary" class="space-y-4">
                <div class="summary-card">
                    <h4 class="font-bold mb-2">Totais Anuais</h4>
                    <div class="flex justify-between mb-1">
                        <span>Receita Total:</span>
                        <span id="annual-total-income" class="text-income">R$ 0,00</span>
                    </div>
                    <div class="flex justify-between">
                        <span>Despesa Total:</span>
                        <span id="annual-total-expense" class="text-expense">R$ 0,00</span>
                    </div>
                </div>
                <div class="summary-card">
                    <h4 class="font-bold mb-2">Despesas Anuais por Categoria</h4>
                    <div id="annual-category-summary" class="space-y-2"></div>
                </div>
                <div class="summary-card">
                    <h4 class="font-bold mb-2">Despesas Anuais por Forma de Pagamento</h4>
                    <div id="annual-payment-summary" class="space-y-2"></div>
                </div>
            </div>
        </div>
    </div>
    
    <div id="configuracoes-tab" class="tab-content space-y-8">
        
        <!-- Configurações -->
        <div class="card">
            <h2 class="text-2xl font-bold mb-4">Configurações</h2>
            
            <div class="summary-card mb-4">
                <form id="add-category-form" class="space-y-2 mb-4">
                    <label class="block text-sm font-medium text-gray-700">Nova Categoria</label>
                    <input type="text" id="new-category" placeholder="Ex: Alimentação" required class="mt-1 block w-full px-3 py-2">
                    <button type="submit" class="w-full bg-gray-500 hover:bg-gray-600 text-white font-bold py-2 px-4 rounded-xl transition duration-300">
                        Adicionar Categoria
                    </button>
                </form>
                <div id="category-list" class="space-y-2"></div>
            </div>

            <div class="summary-card">
                <form id="add-subcategory-form" class="space-y-2 mb-4">
                    <label class="block text-sm font-medium text-gray-700">Categoria Principal</label>
                    <select id="parent-category-select" required class="mt-1 block w-full px-3 py-2">
                        <option value="" disabled selected>Selecione a categoria</option>
                    </select>
                    <label class="block text-sm font-medium text-gray-700">Nova Subcategoria</label>
                    <input type="text" id="new-subcategory" placeholder="Ex: Supermercado" required class="mt-1 block w-full px-3 py-2">
                    <button type="submit" class="w-full bg-gray-500 hover:bg-gray-600 text-white font-bold py-2 px-4 rounded-xl transition duration-300">
                        Adicionar Subcategoria
                    </button>
                </form>
                <div id="subcategory-list" class="space-y-2"></div>
            </div>
        </div>
    </div>
</div>

<!-- Modal de Alerta -->
<div id="alert-modal" class="fixed inset-0 bg-gray-600 bg-opacity-50 hidden flex items-center justify-center p-4">
    <div class="bg-white rounded-lg p-6 shadow-xl w-full max-w-sm">
        <h4 id="alert-title" class="text-lg font-bold mb-2"></h4>
        <p id="alert-message" class="text-gray-700 mb-4"></p>
        <div class="flex justify-end">
            <button id="close-alert-modal" class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-lg">
                OK
            </button>
        </div>
    </div>
</div>

<!-- Modal de Edição -->
<div id="edit-modal" class="fixed inset-0 bg-gray-600 bg-opacity-50 hidden flex items-center justify-center p-4">
    <div class="bg-white rounded-lg p-6 shadow-xl w-full max-w-md space-y-4">
        <h2 class="text-2xl font-bold mb-4">Editar Transação</h2>
        <form id="edit-transaction-form" class="space-y-4">
            <div>
                <label class="block text-sm font-medium text-gray-700">Descrição</label>
                <input type="text" id="edit-description" required class="mt-1 block w-full px-3 py-2">
            </div>
            <!-- Nova linha com duas colunas para Valor e Data -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700">Valor</label>
                    <input type="number" id="edit-amount" step="0.01" required class="mt-1 block w-full px-3 py-2">
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700">Data da Transação</label>
                    <input type="date" id="edit-transaction-date" required class="mt-1 block w-full px-3 py-2">
                </div>
            </div>
            <!-- Nova linha com três colunas para Tipo, Pagador e Categoria -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700">Tipo</label>
                    <select id="edit-type" required class="mt-1 block w-full px-3 py-2">
                        <option value="despesa">Despesa</option>
                        <option value="receita">Receita</option>
                    </select>
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700">Pagador</label>
                    <select id="edit-payer" required class="mt-1 block w-full px-3 py-2">
                        <option value="Lais">Lais</option>
                        <option value="André">André</option>
                        <option value="Lais/André">Lais/André</option>
                    </select>
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700">Categoria</label>
                    <select id="edit-category-select" required class="mt-1 block w-full px-3 py-2"></select>
                </div>
                <div id="edit-subcategory-container" class="hidden">
                    <label class="block text-sm font-medium text-gray-700">Subcategoria</label>
                    <select id="edit-subcategory-select" class="mt-1 block w-full px-3 py-2" required></select>
                </div>
            </div>
             <!-- Nova linha para Forma de Pagamento -->
            <div id="edit-payment-method-container">
                <label class="block text-sm font-medium text-gray-700">Forma de Pagamento</label>
                <select id="edit-payment-method" class="mt-1 block w-full px-3 py-2">
                    <option value="" disabled selected>Selecione a forma de pagamento</option>
                    <option value="Cartão de Crédito">Cartão de Crédito</option>
                    <option value="Cartão de Crédito - Black">Cartão de Crédito - Black</option>
                    <option value="Cartão de Débito">Cartão de Débito</option>
                    <option value="Cartão de Débito - Green">Cartão de Débito - Green</option>
                    <option value="Cartão de Crédito - Flash">Cartão de Crédito - Flash</option>
                    <option value="Dinheiro">Dinheiro</option>
                    <option value="PIX">PIX</option>
                </select>
            </div>
            <div id="price-comparison-transaction" class="hidden bg-gray-100 p-3 rounded-lg mt-4">
                <p id="old-transaction-price" class="text-sm">Valor do Mês Anterior: </p>
                <p id="transaction-price-difference" class="text-sm"></p>
            </div>
            <div class="flex items-center space-x-4">
                <div class="flex items-center space-x-2">
                    <input type="checkbox" id="edit-is-fixed" class="rounded text-blue-500">
                    <label for="edit-is-fixed" class="text-sm font-medium text-gray-700">Despesa Fixa?</label>
                </div>
                <div class="flex items-center space-x-2">
                    <input type="checkbox" id="edit-is-paid" class="rounded text-green-500">
                    <label for="edit-is-paid" class="text-sm font-medium text-gray-700">Pago?</label>
                </div>
            </div>
            <div class="flex justify-end space-x-4">
                <button type="button" id="cancel-edit" class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-xl transition duration-300">
                    Cancelar
                </button>
                <button type="submit" class="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-xl transition duration-300">
                    Salvar Alterações
                </button>
            </div>
        </form>
    </div>
</div>

<!-- Modal de Edição de Item da Lista de Compras -->
<div id="edit-shopping-item-modal" class="fixed inset-0 bg-gray-600 bg-opacity-50 hidden flex items-center justify-center p-4">
    <div class="bg-white rounded-lg p-6 shadow-xl w-full max-w-md space-y-4">
        <h2 class="text-2xl font-bold mb-4">Editar Item da Lista de Compras</h2>
        <form id="edit-shopping-item-form" class="space-y-4">
            <div>
                <label class="block text-sm font-medium text-gray-700">Item</label>
                <input type="text" id="edit-shopping-item" required class="mt-1 block w-full px-3 py-2">
            </div>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700">Valor Esperado</label>
                    <input type="number" id="edit-shopping-value" placeholder="0.00" step="0.01" class="mt-1 block w-full px-3 py-2">
                </div>
                 <div>
                    <label class="block text-sm font-medium text-gray-700">Quantidade</label>
                    <input type="number" id="edit-shopping-quantity" placeholder="1" min="1" value="1" class="mt-1 block w-full px-3 py-2">
                </div>
            </div>
             <div>
                <label class="block text-sm font-medium text-gray-700">Unidade</label>
                <select id="edit-shopping-unit" class="mt-1 block w-full px-3 py-2">
                    <option value="unidade">unidade</option>
                    <option value="kg">kg</option>
                    <option value="litro">litro</option>
                    <option value="pacote">pacote</option>
                </select>
            </div>

            <div id="price-comparison" class="hidden bg-gray-100 p-3 rounded-lg">
                <p id="old-price" class="text-sm">Valor Antigo: </p>
                <p id="price-difference" class="text-sm"></p>
            </div>

            <div class="flex justify-end space-x-4">
                <button type="button" id="cancel-edit-shopping-item" class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-xl transition duration-300">
                    Cancelar
                </button>
                <button type="submit" class="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-xl transition duration-300">
                    Salvar Alterações
                </button>
            </div>
        </form>
    </div>
</div>

<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
    import { getAuth, signInWithCustomToken, signInAnonymously } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
    import { getFirestore, collection, addDoc, onSnapshot, query, where, doc, deleteDoc, updateDoc, getDocs, setDoc, getDoc, getDocFromCache } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
    
    // Variáveis de ambiente fornecidas pelo Canvas
    const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
    const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
    const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;
    
    // Inicialização do Firebase
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);
    const auth = getAuth(app);
    
    let userId = null;
    let transactionsRef = null;
    let categoriesRef = null;
    let subcategoriesRef = null;
    let savingsRef = null;
    let savingsTransactionsRef = null;
    let specificIncomesRef = null;
    let shoppingListRef = null;
    let transactions = [];
    let categories = [];
    let subcategories = [];
    let savingsTransactions = [];
    let shoppingListItems = [];
    let specificIncomeTotals = { 'FLASH': 0, 'GREEN': 0, 'APORTE LAIS': 0, 'APORTE ANDRÉ': 0 };
    let currentMonth = new Date().getMonth();
    let currentYear = new Date().getFullYear();
    const monthNames = ["Janeiro", "Fevereiro", "Março", "Abril", "Maio", "Junho", "Julho", "Agosto", "Setembro", "Outubro", "Novembro", "Dezembro"];
    const categoryColors = {};
    const colorPalette = [
        '#F44336', '#E91E63', '#9C27B0', '#673AB7', '#3F51B5', '#2196F3', '#03A9F4', '#00BCD4',
        '#009688', '#4CAF50', '#8BC34A', '#CDDC39', '#FFEB3B', '#FFC107', '#FF9800', '#FF5722'
    ];
    let nextColorIndex = 0;
    let categoryChart;
    let currentTransactionId = null; // Para armazenar o ID da transação que está sendo editada
    
    // Referências do DOM
    const transactionForm = document.getElementById('transaction-form');
    const categorySelectEl = document.getElementById('category-select');
    const subcategorySelectEl = document.getElementById('subcategory-select');
    const subcategoryContainer = document.getElementById('subcategory-container');
    const monthlySummaryEl = document.getElementById('monthly-summary');
    const annualSummaryEl = document.getElementById('annual-summary');
    const transactionListEl = document.getElementById('transaction-list');
    const currentMonthEl = document.getElementById('current-month');
    const currentYearEl = document.getElementById('current-year');
    const prevMonthBtn = document.getElementById('prev-month');
    const nextMonthBtn = document.getElementById('next-month');
    const prevYearBtn = document.getElementById('prev-year');
    const nextYearBtn = document.getElementById('next-year');
    const alertModal = document.getElementById('alert-modal');
    const alertTitleEl = document.getElementById('alert-title');
    const alertMessageEl = document.getElementById('alert-message');
    const closeAlertModalBtn = document.getElementById('close-alert-modal');
    const isRecurringCheckbox = document.getElementById('is-recurring');
    const recurringMonthsGroup = document.getElementById('recurring-months-group');
    const isInstallmentCheckbox = document.getElementById('is-installment');
    const installmentsGroup = document.getElementById('installments-group');
    const copySummaryBtn = document.getElementById('copy-summary-btn');
    const addCategoryForm = document.getElementById('add-category-form');
    const newCategoryInput = document.getElementById('new-category');
    const categoryListEl = document.getElementById('category-list');
    const subcategoryListEl = document.getElementById('subcategory-list');
    const addSubcategoryForm = document.getElementById('add-subcategory-form');
    const parentCategorySelect = document.getElementById('parent-category-select');
    const newSubcategoryInput = document.getElementById('new-subcategory');
    const paymentMethodSelectEl = document.getElementById('payment-method');
    const paymentMethodContainer = document.getElementById('payment-method-container');
    const savingsBalanceEl = document.getElementById('savings-balance');
    const savingsForm = document.getElementById('savings-form');
    const savingsAmountInput = document.getElementById('savings-amount');
    const savingsPayerSelect = document.getElementById('savings-payer');
    const addToSavingsBtn = document.getElementById('add-to-savings');
    const withdrawFromSavingsBtn = document.getElementById('withdraw-from-savings');
    const savingsTransactionsListEl = document.getElementById('savings-transactions-list');
    const incomeAmountInput = document.getElementById('income-amount');
    const addIncomeToSavingsBtn = document.getElementById('add-income-to-savings');
    const specificIncomeForm = document.getElementById('specific-income-form');
    const specificIncomeTypeSelect = document.getElementById('specific-income-type');
    const specificIncomeAmountInput = document.getElementById('specific-income-amount');
    const specificIncomeSummaryEl = document.getElementById('specific-income-summary');

    const shoppingListForm = document.getElementById('shopping-list-form');
    const shoppingItemInput = document.getElementById('shopping-item');
    const shoppingValueInput = document.getElementById('shopping-value');
    const shoppingListItemsEl = document.getElementById('shopping-list-items');
    const shoppingTotalBoughtEl = document.getElementById('shopping-total-bought');
    const shoppingQuantityInput = document.getElementById('shopping-quantity');
    const shoppingUnitSelect = document.getElementById('shopping-unit');

    // Referências do DOM para o modal de edição
    const editModal = document.getElementById('edit-modal');
    const editForm = document.getElementById('edit-transaction-form');
    const editDescription = document.getElementById('edit-description');
    const editAmount = document.getElementById('edit-amount');
    const editType = document.getElementById('edit-type');
    const editPayer = document.getElementById('edit-payer');
    const editCategorySelectEl = document.getElementById('edit-category-select');
    const editSubcategorySelectEl = document.getElementById('edit-subcategory-select');
    const editSubcategoryContainer = document.getElementById('edit-subcategory-container');
    const editTransactionDate = document.getElementById('edit-transaction-date');
    const editIsFixed = document.getElementById('edit-is-fixed');
    const editIsPaid = document.getElementById('edit-is-paid');
    const cancelEditBtn = document.getElementById('cancel-edit');
    const editPaymentMethodSelectEl = document.getElementById('edit-payment-method');
    const priceComparisonTransactionEl = document.getElementById('price-comparison-transaction');
    const oldTransactionPriceEl = document.getElementById('old-transaction-price');
    const transactionPriceDifferenceEl = document.getElementById('transaction-price-difference');
    const editTransactionPayerEl = document.getElementById('edit-payer'); // Adicionado
    const editTransactionTypeEl = document.getElementById('edit-type'); // Adicionado

    // Referências do DOM para o modal de edição da lista de compras
    const editShoppingItemModal = document.getElementById('edit-shopping-item-modal');
    const editShoppingItemForm = document.getElementById('edit-shopping-item-form');
    const editShoppingItemInput = document.getElementById('edit-shopping-item');
    const editShoppingValueInput = document.getElementById('edit-shopping-value');
    const editShoppingQuantityInput = document.getElementById('edit-shopping-quantity');
    const editShoppingUnitSelect = document.getElementById('edit-shopping-unit');
    const cancelEditShoppingItemBtn = document.getElementById('cancel-edit-shopping-item');
    const priceComparisonEl = document.getElementById('price-comparison');
    const oldPriceEl = document.getElementById('old-price');
    const priceDifferenceEl = document.getElementById('price-difference');
    let currentShoppingItemId = null;
    let oldShoppingValue = null;
    
    const filterPayerSelect = document.getElementById('filter-payer');
    const filterCategorySelect = document.getElementById('filter-category');
    const filterSubcategorySelect = document.getElementById('filter-subcategory');
    const generateTransactionBtn = document.getElementById('generate-transaction-btn');
    
    // Funções de UI
    function showAlert(title, message) {
        alertTitleEl.textContent = title;
        alertMessageEl.textContent = message;
        alertModal.classList.remove('hidden');
    }
    
    closeAlertModalBtn.addEventListener('click', () => {
        alertModal.classList.add('hidden');
    });
    
    cancelEditBtn.addEventListener('click', () => {
        editModal.classList.add('hidden');
    });

    cancelEditShoppingItemBtn.addEventListener('click', () => {
        editShoppingItemModal.classList.add('hidden');
    });
    
    // Esconder/mostrar campos com base no tipo de transação
    function togglePaymentMethodField(transactionType) {
        if (transactionType === 'receita') {
            paymentMethodContainer.classList.add('hidden');
            paymentMethodSelectEl.required = false;
        } else {
            paymentMethodContainer.classList.remove('hidden');
            paymentMethodSelectEl.required = true;
        }
    }
    
    function toggleEditPaymentMethodField(transactionType) {
        if (transactionType === 'receita') {
            document.getElementById('edit-payment-method-container').classList.add('hidden');
            editPaymentMethodSelectEl.required = false;
        } else {
            document.getElementById('edit-payment-method-container').classList.remove('hidden');
            editPaymentMethodSelectEl.required = true;
        }
    }
    
    document.getElementById('type').addEventListener('change', (e) => {
        togglePaymentMethodField(e.target.value);
    });
    
    document.getElementById('edit-type').addEventListener('change', (e) => {
        toggleEditPaymentMethodField(e.target.value);
    });
    
    // Toggle para mostrar/esconder o campo de meses recorrentes
    isRecurringCheckbox.addEventListener('change', (e) => {
        if (e.target.checked) {
            recurringMonthsGroup.classList.remove('hidden');
        } else {
            recurringMonthsGroup.classList.add('hidden');
        }
    });
    
    // Toggle para mostrar/esconder o campo de parcelas
    isInstallmentCheckbox.addEventListener('change', (e) => {
        if (e.target.checked) {
            installmentsGroup.classList.remove('hidden');
        } else {
            installmentsGroup.classList.add('hidden');
        }
    });
    
    // Função para obter uma cor consistente para cada categoria
    function getCategoryColor(category) {
        if (!categoryColors[category]) {
            categoryColors[category] = colorPalette[nextColorIndex % colorPalette.length];
            nextColorIndex++;
        }
        return categoryColors[category];
    }
    
    // Função para renderizar o gráfico
    function renderCategoryChart(categoryExpenses, totalExpense) {
        const labels = Object.keys(categoryExpenses);
        const data = Object.values(categoryExpenses);
        const percentages = data.map(value => totalExpense > 0 ? ((value / totalExpense) * 100).toFixed(2) : 0);
        const colors = labels.map(label => getCategoryColor(label));
    
        const chartData = {
            labels: labels,
            datasets: [{
                label: 'Valor Gasto',
                data: data,
                backgroundColor: colors,
            }]
        };
    
        const config = {
            type: 'bar',
            data: chartData,
            options: {
                responsive: true,
                maintainAspectRatio: false,
                plugins: {
                    legend: {
                        display: false,
                    },
                    title: {
                        display: true,
                        text: 'Despesas por Categoria'
                    },
                    tooltip: {
                        callbacks: {
                            afterLabel: function(context) {
                                const index = context.dataIndex;
                                return `Percentual: ${percentages[index]}%`;
                            }
                        }
                    }
                },
                scales: {
                    y: {
                        beginAtZero: true
                    }
                },
            },
        };
    
        const ctx = document.getElementById('categoryChart').getContext('2d');
        if (categoryChart) {
            categoryChart.destroy();
        }
        categoryChart = new Chart(ctx, config);
    }
    
    // Funções de renderização
    function renderMonthlySummary() {
        const filteredTransactions = transactions.filter(t => {
            const date = new Date(t.date);
            return date.getMonth() === currentMonth && date.getFullYear() === currentYear;
        });
    
        let totalIncome = 0;
        let totalExpense = 0;
        const categoryExpenses = {};
        const subcategoryExpenses = {};
        const payerExpenses = { 'Lais': 0, 'André': 0, 'Lais/André': 0 };
        const paymentMethodExpenses = {};

        filteredTransactions.forEach(t => {
            const amount = parseFloat(t.amount);
            if (t.type === 'receita') {
                totalIncome += amount;
            } else {
                totalExpense += amount;
                if (!categoryExpenses[t.mainCategory]) {
                    categoryExpenses[t.mainCategory] = 0;
                }
                categoryExpenses[t.mainCategory] += amount;

                if (t.subcategory) {
                    if (!subcategoryExpenses[t.mainCategory]) {
                        subcategoryExpenses[t.mainCategory] = {};
                    }
                    if (!subcategoryExpenses[t.mainCategory][t.subcategory]) {
                        subcategoryExpenses[t.mainCategory][t.subcategory] = 0;
                    }
                    subcategoryExpenses[t.mainCategory][t.subcategory] += amount;
                }

                if (t.paymentMethod) {
                    if (!paymentMethodExpenses[t.paymentMethod]) {
                        paymentMethodExpenses[t.paymentMethod] = 0;
                    }
                    paymentMethodExpenses[t.paymentMethod] += amount;
                }
    
                if (t.payer === 'Lais/André') {
                    payerExpenses['Lais/André'] += amount;
                    payerExpenses['Lais'] += amount / 2;
                    payerExpenses['André'] += amount / 2;
                } else {
                    payerExpenses[t.payer] += amount;
                }
            }
        });
    
        document.getElementById('total-income').textContent = `R$ ${totalIncome.toFixed(2).replace('.', ',')}`;
        document.getElementById('total-expense').textContent = `R$ ${totalExpense.toFixed(2).replace('.', ',')}`;
        
        // Renderizar resumo de categorias
        const monthlyCategorySummaryEl = document.getElementById('monthly-category-summary');
        monthlyCategorySummaryEl.innerHTML = Object.entries(categoryExpenses).map(([category, amount]) => {
            const percentage = (amount / totalExpense) * 100;
            const subcategoriesHtml = Object.entries(subcategoryExpenses[category] || {}).map(([subcat, subAmount]) => {
                const subPercentage = (subAmount / amount) * 100;
                return `
                    <div class="flex justify-between text-sm text-gray-600">
                        <span>- ${subcat}:</span>
                        <span>R$ ${subAmount.toFixed(2).replace('.', ',')} (${subPercentage.toFixed(2)}%)</span>
                    </div>
                `;
            }).join('');
            
            return `
                <div class="card p-4 my-2">
                    <div class="flex items-center justify-between cursor-pointer" onclick="toggleSubcategory('${category}')">
                        <div class="flex items-center">
                            <div class="category-color-box" style="background-color: ${getCategoryColor(category)};"></div>
                            <span class="flex-grow font-bold">${category}:</span>
                        </div>
                        <div class="flex items-center">
                            <span class="font-bold">R$ ${amount.toFixed(2).replace('.', ',')} (${percentage.toFixed(2)}%)</span>
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 ml-2 transform transition-transform" viewBox="0 0 20 20" fill="currentColor">
                                <path fill-rule="evenodd" d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z" clip-rule="evenodd" />
                            </svg>
                        </div>
                    </div>
                    <div id="subcategories-${category.replace(/\s/g, '-')}" class="pl-8 mt-2 space-y-1 hidden">
                        ${subcategoriesHtml}
                    </div>
                </div>
            `;
        }).join('');
    
        // Renderizar resumo por pagador
        const payerSummaryEl = document.getElementById('payer-summary');
        payerSummaryEl.innerHTML = `
            <div class="flex justify-between">
                <span class="font-bold">Lais:</span>
                <span>R$ ${payerExpenses['Lais'].toFixed(2).replace('.', ',')}</span>
            </div>
            <div class="flex justify-between">
                <span class="font-bold">André:</span>
                <span>R$ ${payerExpenses['André'].toFixed(2).replace('.', ',')}</span>
            </div>
            <div class="flex justify-between">
                <span class="font-bold">Lais/André:</span>
                <span>R$ ${payerExpenses['Lais/André'].toFixed(2).replace('.', ',')}</span>
            </div>
        `;

        // Renderizar resumo por forma de pagamento
        const paymentSummaryEl = document.getElementById('payment-summary');
        paymentSummaryEl.innerHTML = Object.entries(paymentMethodExpenses).map(([method, amount]) => `
            <div class="flex justify-between">
                <span>${method}:</span>
                <span class="font-bold">R$ ${amount.toFixed(2).replace('.', ',')}</span>
            </div>
        `).join('');
    
        // Renderizar o gráfico
        renderCategoryChart(categoryExpenses, totalExpense);
    
        renderTransactionList();
    }

    window.toggleSubcategory = (category) => {
        const subcategoryList = document.getElementById(`subcategories-${category.replace(/\s/g, '-')}`);
        const icon = subcategoryList.previousElementSibling.querySelector('svg');
        if (subcategoryList.classList.contains('hidden')) {
            subcategoryList.classList.remove('hidden');
            icon.classList.remove('rotate-0');
            icon.classList.add('rotate-180');
        } else {
            subcategoryList.classList.add('hidden');
            icon.classList.remove('rotate-180');
            icon.classList.add('rotate-0');
        }
    }
    
    function renderTransactionList() {
        const selectedPayer = filterPayerSelect.value;
        const selectedCategory = filterCategorySelect.value;
        const selectedSubcategory = filterSubcategorySelect.value;
        const today = new Date();
        today.setHours(0, 0, 0, 0);

        const filteredTransactions = transactions.filter(t => {
            const date = new Date(t.date);
            const isMonthly = date.getMonth() === currentMonth && date.getFullYear() === currentYear;
            const isPayerFiltered = selectedPayer === 'all' || t.payer === selectedPayer;
            const isCategoryFiltered = selectedCategory === 'all' || t.mainCategory === selectedCategory;
            const isSubcategoryFiltered = selectedSubcategory === 'all' || t.subcategory === selectedSubcategory;
            return isMonthly && isPayerFiltered && isCategoryFiltered && isSubcategoryFiltered;
        });

        if (filteredTransactions.length === 0) {
            transactionListEl.innerHTML = `
                <div class="text-center text-gray-500 py-4">
                    Nenhuma transação encontrada para este mês.
                </div>
            `;
        } else {
            transactionListEl.innerHTML = filteredTransactions.sort((a, b) => new Date(b.date) - new Date(a.date)).map(t => {
                const isOverdue = t.type === 'despesa' && !t.isPaid && new Date(t.date) < today;
                const amountClass = isOverdue ? 'text-overdue' : (t.type === 'receita' ? 'text-income' : 'text-expense');
                const statusText = t.isPaid ? `<span class="text-paid">Pago</span>` : (isOverdue ? `<span class="text-overdue">Vencido</span>` : 'Em Aberto');
                const isExpense = t.type === 'despesa';
                
                return `
                <div class="card p-4 my-2 flex justify-between items-center transition duration-300 transform hover:scale-105">
                    <div>
                        <div class="text-sm font-semibold">${t.description}</div>
                        <div class="text-xs text-gray-500">
                            ${t.isFixed ? 'Fixa' : 'Variável'} | ${t.payer} | ${t.mainCategory} ${t.subcategory ? `| ${t.subcategory}` : ''} | ${new Date(t.date).toLocaleDateString('pt-BR')} | ${t.paymentMethod || 'N/A'} ${isExpense ? `| ${statusText}` : ''}
                        </div>
                    </div>
                    <div class="flex items-center space-x-2">
                        <span class="font-bold ${amountClass}">
                            ${t.type === 'receita' ? '+' : '-'} R$ ${parseFloat(t.amount).toFixed(2).replace('.', ',')}
                        </span>
                        <button class="text-gray-500 hover:text-gray-700 text-xl" onclick="editTransaction('${t.id}')">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                              <path d="M17.414 2.586a2 2 0 00-2.828 0L7 10.172V13h2.828l7.586-7.586a2 2 0 000-2.828z" />
                              <path fill-rule="evenodd" d="M2 6a2 2 0 012-2h4a1 1 0 010 2H4v10h10v-4a1 1 0 112 0v4a2 2 0 01-2 2H4a2 2 0 01-2-2V6z" clip-rule="evenodd" />
                            </svg>
                        </button>
                        <button class="text-red-500 hover:text-red-700 text-xl" onclick="deleteTransaction('${t.id}')">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                              <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm6 0a1 1 0 11-2 0v6a1 1 0 112 0V8z" clip-rule="evenodd" />
                            </svg>
                        </button>
                    </div>
                </div>
            `;
            }).join('');
        }
    }
    
    function renderAnnualSummary() {
        const filteredTransactions = transactions.filter(t => {
            const date = new Date(t.date);
            return date.getFullYear() === currentYear;
        });
    
        let annualTotalIncome = 0;
        let annualTotalExpense = 0;
        const annualCategoryExpenses = {};
        const annualPaymentExpenses = {};
    
        filteredTransactions.forEach(t => {
            const amount = parseFloat(t.amount);
            if (t.type === 'receita') {
                annualTotalIncome += amount;
            } else {
                annualTotalExpense += amount;
                if (!annualCategoryExpenses[t.mainCategory]) {
                    annualCategoryExpenses[t.mainCategory] = 0;
                }
                annualCategoryExpenses[t.mainCategory] += amount;

                if (t.paymentMethod) {
                    if (!annualPaymentExpenses[t.paymentMethod]) {
                        annualPaymentExpenses[t.paymentMethod] = 0;
                    }
                    annualPaymentExpenses[t.paymentMethod] += amount;
                }
            }
        });
    
        document.getElementById('annual-total-income').textContent = `R$ ${annualTotalIncome.toFixed(2).replace('.', ',')}`;
        document.getElementById('annual-total-expense').textContent = `R$ ${annualTotalExpense.toFixed(2).replace('.', ',')}`;
    
        const annualCategorySummaryEl = document.getElementById('annual-category-summary');
        annualCategorySummaryEl.innerHTML = Object.entries(annualCategoryExpenses).sort(([, a], [, b]) => b - a).map(([category, amount]) => `
            <div class="category-item">
                <div class="category-color-box" style="background-color: ${getCategoryColor(category)};"></div>
                <span class="flex-grow">${category}:</span>
                <span class="font-bold">R$ ${amount.toFixed(2).replace('.', ',')}</span>
            </div>
        `).join('');

        const annualPaymentSummaryEl = document.getElementById('annual-payment-summary');
        annualPaymentSummaryEl.innerHTML = Object.entries(annualPaymentExpenses).sort(([, a], [, b]) => b - a).map(([payment, amount]) => `
            <div class="flex justify-between">
                <span>${payment}:</span>
                <span class="font-bold">R$ ${amount.toFixed(2).replace('.', ',')}</span>
            </div>
        `).join('');
    }
    
    function updateDateDisplay() {
        currentMonthEl.textContent = `${monthNames[currentMonth]} de ${currentYear}`;
        currentYearEl.textContent = `${currentYear}`;
    }
    
    // Funções para lidar com as categorias
    function renderCategoryDropdowns() {
        categorySelectEl.innerHTML = '<option value="" disabled selected>Selecione uma categoria</option>';
        editCategorySelectEl.innerHTML = '<option value="" disabled selected>Selecione uma categoria</option>';
        parentCategorySelect.innerHTML = '<option value="" disabled selected>Selecione a categoria</option>';
        filterCategorySelect.innerHTML = '<option value="all">Todas as Categorias</option>';
    
        categories.forEach(cat => {
            const option = document.createElement('option');
            option.value = cat.name;
            option.textContent = cat.name;
            categorySelectEl.appendChild(option);
            parentCategorySelect.appendChild(option.cloneNode(true));
            editCategorySelectEl.appendChild(option.cloneNode(true));
            filterCategorySelect.appendChild(option.cloneNode(true));
        });
    
        // Evento para preencher o dropdown de subcategorias
        categorySelectEl.addEventListener('change', () => {
            populateSubcategoryDropdown(categorySelectEl.value);
            subcategoryContainer.classList.remove('hidden');
        });
    
        editCategorySelectEl.addEventListener('change', () => {
            populateEditSubcategoryDropdown(editCategorySelectEl.value);
            editSubcategoryContainer.classList.remove('hidden');
        });

        filterCategorySelect.addEventListener('change', () => {
            populateFilterSubcategoryDropdown(filterCategorySelect.value);
            renderTransactionList();
        });
    }

    function populateFilterSubcategoryDropdown(mainCategoryName) {
        filterSubcategorySelect.innerHTML = '<option value="all">Todas as Subcategorias</option>';
        filterSubcategorySelect.disabled = true;
        const relevantSubcategories = subcategories.filter(sub => sub.mainCategory === mainCategoryName);
        relevantSubcategories.sort((a, b) => a.name.localeCompare(b.name, 'pt-BR'));
        if (relevantSubcategories.length > 0) {
            filterSubcategorySelect.disabled = false;
            relevantSubcategories.forEach(sub => {
                const option = document.createElement('option');
                option.value = sub.name;
                option.textContent = sub.name;
                filterSubcategorySelect.appendChild(option);
            });
        }
    }
    
    function populateSubcategoryDropdown(mainCategoryName) {
        subcategorySelectEl.innerHTML = '';
        const relevantSubcategories = subcategories.filter(sub => sub.mainCategory === mainCategoryName);
        relevantSubcategories.sort((a, b) => a.name.localeCompare(b.name, 'pt-BR'));
        if (relevantSubcategories.length > 0) {
            subcategorySelectEl.innerHTML = `<option value="" disabled selected>Selecione uma subcategoria</option>`;
            subcategoryContainer.classList.remove('hidden');
            relevantSubcategories.forEach(sub => {
                const option = document.createElement('option');
                option.value = sub.name;
                option.textContent = sub.name;
                subcategorySelectEl.appendChild(option);
            });
        } else {
            subcategoryContainer.classList.add('hidden');
        }
    }
    
    function populateEditSubcategoryDropdown(mainCategoryName) {
        editSubcategorySelectEl.innerHTML = '';
        const relevantSubcategories = subcategories.filter(sub => sub.mainCategory === mainCategoryName);
        relevantSubcategories.sort((a, b) => a.name.localeCompare(b.name, 'pt-BR'));
        if (relevantSubcategories.length > 0) {
            editSubcategorySelectEl.innerHTML = `<option value="" disabled selected>Selecione uma subcategoria</option>`;
            editSubcategoryContainer.classList.remove('hidden');
            relevantSubcategories.forEach(sub => {
                const option = document.createElement('option');
                option.value = sub.name;
                option.textContent = sub.name;
                editSubcategorySelectEl.appendChild(option);
            });
        } else {
            editSubcategoryContainer.classList.add('hidden');
        }
    }

    function renderCategoryList() {
        categoryListEl.innerHTML = categories.map(cat => `
            <div class="flex items-center justify-between">
                <span>${cat.name}</span>
                <button onclick="deleteCategory('${cat.id}')" class="text-red-500 hover:text-red-700 text-xl">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                      <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm6 0a1 1 0 11-2 0v6a1 1 0 112 0V8z" clip-rule="evenodd" />
                    </svg>
                </button>
            </div>
        `).join('');
    }

    function renderSubcategoryList() {
        subcategoryListEl.innerHTML = subcategories.map(sub => `
            <div class="flex items-center justify-between">
                <span class="text-sm text-gray-700">${sub.name} (${sub.mainCategory})</span>
                <button onclick="deleteSubcategory('${sub.id}')" class="text-red-500 hover:text-red-700 text-xl">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                      <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm6 0a1 1 0 11-2 0v6a1 1 0 112 0V8z" clip-rule="evenodd" />
                    </svg>
                </button>
            </div>
        `).join('');
    }

    window.deleteCategory = async (id) => {
        try {
            const categoryDocRef = doc(categoriesRef, id);
            await deleteDoc(categoryDocRef);
        } catch (error) {
            console.error("Erro ao deletar categoria:", error);
            showAlert("Erro", "Não foi possível deletar a categoria. Tente novamente.");
        }
    }

    window.deleteSubcategory = async (id) => {
        try {
            const subcategoryDocRef = doc(subcategoriesRef, id);
            await deleteDoc(subcategoryDocRef);
        } catch (error) {
            console.error("Erro ao deletar subcategoria:", error);
            showAlert("Erro", "Não foi possível deletar a subcategoria. Tente novamente.");
        }
    }
    
    function fetchData() {
        if (!transactionsRef) {
            console.error("Referência da coleção de transações não está definida.");
            return;
        }
    
        const qTrans = query(transactionsRef);
        onSnapshot(qTrans, (snapshot) => {
            transactions = [];
            snapshot.forEach(doc => {
                const data = doc.data();
                transactions.push({ id: doc.id, ...data, date: data.date.toDate().toISOString() });
            });
            renderMonthlySummary();
            renderAnnualSummary();
        });
    
        if (!categoriesRef) {
             console.error("Referência da coleção de categorias não está definida.");
             return;
        }
    
        const qCat = query(categoriesRef);
        onSnapshot(qCat, (snapshot) => {
            categories = [];
            snapshot.forEach(doc => {
                categories.push({ id: doc.id, ...doc.data() });
            });
            categories.sort((a, b) => a.name.localeCompare(b.name, 'pt-BR'));
            renderCategoryDropdowns();
            renderCategoryList();
        });

        if (!subcategoriesRef) {
            console.error("Referência da coleção de subcategorias não está definida.");
            return;
        }
    
        const qSubcat = query(subcategoriesRef);
        onSnapshot(qSubcat, (snapshot) => {
            subcategories = [];
            snapshot.forEach(doc => {
                subcategories.push({ id: doc.id, ...doc.data() });
            });
            subcategories.sort((a, b) => a.name.localeCompare(b.name, 'pt-BR'));
            renderSubcategoryList();
        });

        // Listener para o saldo da caixinha
        const qSavings = query(savingsRef);
        onSnapshot(qSavings, (snapshot) => {
            let savingsBalance = 0;
            snapshot.forEach(doc => {
                const data = doc.data();
                savingsBalance += data.amount;
            });
            savingsBalanceEl.textContent = `R$ ${savingsBalance.toFixed(2).replace('.', ',')}`;
        });
        
        // Listener para as transações da caixinha
        const qSavingsTrans = query(savingsTransactionsRef);
        onSnapshot(qSavingsTrans, (snapshot) => {
            savingsTransactions = [];
            snapshot.forEach(doc => {
                const data = doc.data();
                savingsTransactions.push({ id: doc.id, ...data, date: data.date.toDate().toISOString() });
            });
            renderSavingsTransactionsList();
        });

        // Listener para os saldos de receitas específicas
        const qSpecificIncomes = query(specificIncomesRef);
        onSnapshot(qSpecificIncomes, (snapshot) => {
            specificIncomeTotals = { 'FLASH': 0, 'GREEN': 0, 'APORTE LAIS': 0, 'APORTE ANDRÉ': 0 };
            snapshot.forEach(doc => {
                const data = doc.data();
                if (specificIncomeTotals.hasOwnProperty(data.type)) {
                    specificIncomeTotals[data.type] = data.amount;
                }
            });
            renderSpecificIncomeSummary();
        });

        // Listener para a lista de compras
        const qShoppingList = query(shoppingListRef);
        onSnapshot(qShoppingList, (snapshot) => {
            shoppingListItems = [];
            snapshot.forEach(doc => {
                shoppingListItems.push({ id: doc.id, ...doc.data() });
            });
            renderShoppingList();
        });
    }

    function renderSavingsTransactionsList() {
        if (savingsTransactions.length === 0) {
            savingsTransactionsListEl.innerHTML = `
                <div class="text-center text-gray-500 py-2">
                    Nenhuma transação na caixinha.
                </div>
            `;
        } else {
            savingsTransactionsListEl.innerHTML = savingsTransactions.sort((a, b) => new Date(b.date) - new Date(a.date)).map(t => {
                let amountClass;
                let sign;
                if (t.type === 'entrada' || t.type === 'rendimento') {
                    amountClass = 'text-income';
                    sign = '+';
                } else {
                    amountClass = 'text-expense';
                    sign = '-';
                }
                const description = t.description;
                const date = new Date(t.date).toLocaleDateString('pt-BR');
                return `
                    <div class="flex justify-between items-center text-sm">
                        <span>${date} - ${description}</span>
                        <span class="font-bold ${amountClass}">${sign} R$ ${parseFloat(t.amount).toFixed(2).replace('.', ',')}</span>
                    </div>
                `;
            }).join('');
        }
    }

    async function renderShoppingList() {
        shoppingListItemsEl.innerHTML = '';
        let totalBought = 0;
        for (const item of shoppingListItems) {
            const oldPriceComparison = item.priceDifference !== undefined ? `
                <p class="text-xs ${item.priceDifference >= 0 ? 'text-red-500' : 'text-green-500'}">
                    ${item.priceDifference >= 0 ? 'Aumento' : 'Redução'} de R$ ${Math.abs(item.priceDifference).toFixed(2).replace('.', ',')} (${Math.abs(item.pricePercentage).toFixed(2)}%)
                </p>
            ` : '';
            
            const isBought = item.isBought;
            const html = `
                <div class="flex items-center justify-between card p-4 my-2">
                    <div class="flex items-center">
                        ${!isBought ? `<input type="checkbox" data-id="${item.id}" class="mr-3 rounded text-green-500">` : ''}
                        <div>
                            <span class="font-semibold ${isBought ? 'text-gray-500' : ''}">${item.name}</span>
                            ${item.value ? `<span class="text-sm text-gray-500 ml-2">(R$ ${item.value.toFixed(2).replace('.', ',')} x ${item.quantity || 1})</span>` : ''}
                            ${item.value ? `<span class="text-sm text-gray-500 ml-2">Total: R$ ${(item.value * (item.quantity || 1)).toFixed(2).replace('.', ',')}</span>` : ''}
                            ${oldPriceComparison}
                        </div>
                    </div>
                    <div class="flex items-center space-x-2">
                         <button class="text-gray-500 hover:text-gray-700 text-xl" onclick="editShoppingItem('${item.id}')">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                              <path d="M17.414 2.586a2 2 0 00-2.828 0L7 10.172V13h2.828l7.586-7.586a2 2 0 000-2.828z" />
                              <path fill-rule="evenodd" d="M2 6a2 2 0 012-2h4a1 1 0 010 2H4v10h10v-4a1 1 0 112 0v4a2 2 0 01-2 2H4a2 2 0 01-2-2V6z" clip-rule="evenodd" />
                            </svg>
                        </button>
                        <button onclick="deleteShoppingItem('${item.id}')" class="text-red-500 hover:text-red-700 text-xl">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                                <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm6 0a1 1 0 11-2 0v6a1 1 0 112 0V8z" clip-rule="evenodd" />
                            </svg>
                        </button>
                    </div>
                </div>
            `;
            shoppingListItemsEl.innerHTML += html;
            if (isBought && item.value && item.quantity) {
                totalBought += item.value * item.quantity;
            } else if (isBought && item.value) {
                totalBought += item.value;
            }
        }
        shoppingTotalBoughtEl.querySelector('span:last-child').textContent = `R$ ${totalBought.toFixed(2).replace('.', ',')}`;
    }
    
    // Altera a função para apenas atualizar o estado do checkbox
    window.toggleShoppingItemCheckbox = async (id, isChecked) => {
        try {
            const itemRef = doc(shoppingListRef, id);
            await updateDoc(itemRef, { isBought: isChecked });
        } catch (error) {
            console.error("Erro ao atualizar item da lista de compras:", error);
            showAlert("Erro", "Não foi possível atualizar o item. Tente novamente.");
        }
    }

    // Altera a função createTransactionFromShoppingItem para processar múltiplos itens selecionados
    window.createTransactionFromShoppingItem = async () => {
        const checkboxes = document.querySelectorAll('#shopping-list-items input[type="checkbox"]');
        const selectedItems = [];
        checkboxes.forEach(checkbox => {
            if (checkbox.checked) {
                const itemId = checkbox.dataset.id;
                const item = shoppingListItems.find(i => i.id === itemId);
                if (item) {
                    selectedItems.push(item);
                }
            }
        });

        if (selectedItems.length === 0) {
            showAlert("Erro", "Por favor, selecione pelo menos um item para gerar o lançamento.");
            return;
        }

        const totalAmount = selectedItems.reduce((total, item) => total + ((item.value || 0) * (item.quantity || 1)), 0);
        const itemDescriptions = selectedItems.map(item => `${item.name} (R$ ${(item.value || 0).toFixed(2).replace('.', ',')} x ${item.quantity || 1})`).join(', ');

        try {
            // Adicionar a nova transação
            await addDoc(transactionsRef, {
                description: `Itens da Lista: ${itemDescriptions}`,
                amount: totalAmount,
                type: 'despesa',
                payer: 'Lais/André', // Pagador padrão
                mainCategory: 'Compras',
                subcategory: 'Itens da Lista',
                paymentMethod: null,
                isFixed: false,
                isPaid: true,
                date: new Date(),
                userId: userId,
            });

            // Marcar os itens selecionados como comprados na lista de compras
            for (const item of selectedItems) {
                const itemRef = doc(shoppingListRef, item.id);
                await updateDoc(itemRef, { isBought: false });
            }

            showAlert("Sucesso", `Lançamento para os itens selecionados criado com sucesso!`);
        } catch (error) {
            console.error("Erro ao criar lançamento da lista de compras:", error);
            showAlert("Erro", "Não foi possível criar o lançamento. Tente novamente.");
        }
    };
    generateTransactionBtn.addEventListener('click', createTransactionFromShoppingItem);


    window.deleteShoppingItem = async (id) => {
        try {
            const itemRef = doc(shoppingListRef, id);
            await deleteDoc(itemRef);
        } catch (error) {
            console.error("Erro ao deletar item da lista de compras:", error);
            showAlert("Erro", "Não foi possível deletar o item. Tente novamente.");
        }
    }

    window.editShoppingItem = (id) => {
        const itemToEdit = shoppingListItems.find(item => item.id === id);
        if (itemToEdit) {
            currentShoppingItemId = id;
            editShoppingItemInput.value = itemToEdit.name;
            editShoppingValueInput.value = itemToEdit.value;
            editShoppingQuantityInput.value = itemToEdit.quantity || 1;
            editShoppingUnitSelect.value = itemToEdit.unit;
            oldShoppingValue = itemToEdit.value;
            editShoppingItemModal.classList.remove('hidden');
        }
    };
    
    // Evento de envio do formulário de edição de item da lista de compras
    editShoppingItemForm.addEventListener('submit', async (e) => {
        e.preventDefault();
        
        const updatedData = {
            name: editShoppingItemInput.value,
            value: parseFloat(editShoppingValueInput.value),
            quantity: parseFloat(editShoppingQuantityInput.value),
            unit: editShoppingUnitSelect.value,
        };

        const difference = updatedData.value - oldShoppingValue;
        const percentage = (difference / oldShoppingValue) * 100;

        updatedData.priceDifference = difference;
        updatedData.pricePercentage = percentage;

        try {
            const itemDocRef = doc(shoppingListRef, currentShoppingItemId);
            await updateDoc(itemDocRef, updatedData);
            editShoppingItemModal.classList.add('hidden');
            showAlert("Sucesso", "Item da lista de compras atualizado com sucesso!");
        } catch (error) {
            console.error("Erro ao atualizar item da lista de compras:", error);
            showAlert("Erro", "Não foi possível atualizar o item. Tente novamente.");
        }
    });

    // Função para abrir o modal de edição e preencher os campos
    window.editTransaction = async (id) => {
        const transactionToEdit = transactions.find(t => t.id === id);
        if (transactionToEdit) {
            currentTransactionId = id;
            editDescription.value = transactionToEdit.description;
            editAmount.value = transactionToEdit.amount;
            editType.value = transactionToEdit.type;
            editPayer.value = transactionToEdit.payer;
            editCategorySelectEl.value = transactionToEdit.mainCategory;
            toggleEditPaymentMethodField(transactionToEdit.type);
            populateEditSubcategoryDropdown(transactionToEdit.mainCategory);
            editSubcategorySelectEl.value = transactionToEdit.subcategory;
            // Corrigir a data para o fuso horário local
            const localDate = new Date(transactionToEdit.date);
            localDate.setMinutes(localDate.getMinutes() - localDate.getTimezoneOffset());
            editTransactionDate.value = localDate.toISOString().slice(0, 10);
            editIsFixed.checked = transactionToEdit.isFixed;
            editIsPaid.checked = transactionToEdit.isPaid;
            editPaymentMethodSelectEl.value = transactionToEdit.paymentMethod || '';
            
            // Lógica para mostrar comparação de preço para despesas fixas
            priceComparisonTransactionEl.classList.add('hidden');
            if (transactionToEdit.isFixed) {
                const prevMonth = currentMonth === 0 ? 11 : currentMonth - 1;
                const prevYear = currentMonth === 0 ? currentYear - 1 : currentYear;

                const previousTransaction = transactions.find(t => 
                    t.description === transactionToEdit.description &&
                    t.mainCategory === transactionToEdit.mainCategory &&
                    t.subcategory === transactionToEdit.subcategory &&
                    new Date(t.date).getMonth() === prevMonth &&
                    new Date(t.date).getFullYear() === prevYear
                );

                if (previousTransaction) {
                    const oldAmount = parseFloat(previousTransaction.amount);
                    const newAmount = parseFloat(transactionToEdit.amount);
                    const difference = newAmount - oldAmount;
                    const percentage = (difference / oldAmount) * 100;

                    oldTransactionPriceEl.textContent = `Valor do Mês Anterior: R$ ${oldAmount.toFixed(2).replace('.', ',')}`;
                    transactionPriceDifferenceEl.textContent = `${difference >= 0 ? 'Aumento' : 'Redução'} de R$ ${Math.abs(difference).toFixed(2).replace('.', ',')} (${Math.abs(percentage).toFixed(2)}%)`;
                    transactionPriceDifferenceEl.className = `text-sm ${difference >= 0 ? 'text-red-500' : 'text-green-500'}`;
                    priceComparisonTransactionEl.classList.remove('hidden');
                }
            }

            editModal.classList.remove('hidden');
        }
    };

    // Função para descontar do saldo de receitas específicas
    async function updateSpecificIncomeBalance(type, amount) {
        try {
            const incomeDocRef = doc(specificIncomesRef, type);
            const incomeDoc = await getDoc(incomeDocRef);
            if (incomeDoc.exists()) {
                const currentAmount = incomeDoc.data().amount;
                await updateDoc(incomeDocRef, { amount: currentAmount + amount });
            } else {
                await setDoc(incomeDocRef, { type: type, amount: amount });
            }
        } catch (error) {
            console.error(`Erro ao atualizar saldo de ${type}:`, error);
        }
    }
    
    // Função para copiar o resumo para a área de transferência
    copySummaryBtn.addEventListener('click', () => {
        const selectedPayer = filterPayerSelect.value;
        const selectedCategory = filterCategorySelect.value;
        const selectedSubcategory = filterSubcategorySelect.value;
        const filteredTransactions = transactions.filter(t => {
            const date = new Date(t.date);
            const isMonthly = date.getMonth() === currentMonth && date.getFullYear() === currentYear;
            const isPayerFiltered = selectedPayer === 'all' || t.payer === selectedPayer;
            const isCategoryFiltered = selectedCategory === 'all' || t.mainCategory === selectedCategory;
            const isSubcategoryFiltered = selectedSubcategory === 'all' || t.subcategory === selectedSubcategory;
            return isMonthly && isPayerFiltered && isCategoryFiltered && isSubcategoryFiltered;
        });
    
        let totalIncome = 0;
        let totalExpense = 0;
        const payerExpenses = { 'Lais': 0, 'André': 0, 'Lais/André': 0 };
        const transactionList = [];
    
        filteredTransactions.forEach(t => {
            const amount = parseFloat(t.amount);
            if (t.type === 'receita') {
                totalIncome += amount;
            } else {
                totalExpense += amount;
                if (t.payer === 'Lais/André') {
                    payerExpenses['Lais/André'] += amount;
                    payerExpenses['Lais'] += amount / 2;
                    payerExpenses['André'] += amount / 2;
                } else {
                    payerExpenses[t.payer] += amount;
                }
            }
            transactionList.push(`${t.description} (${t.mainCategory}${t.subcategory ? `/${t.subcategory}` : ''}) - R$ ${amount.toFixed(2).replace('.', ',')} (${t.type === 'receita' ? 'Receita' : 'Despesa'})`);
        });
    
        const summaryText = `
Resumo Mensal - ${monthNames[currentMonth]} de ${currentYear}
Pagador Selecionado: ${selectedPayer === 'all' ? 'Todos' : selectedPayer}
${selectedCategory !== 'all' ? `Categoria Selecionada: ${selectedCategory}` : ''}
${selectedSubcategory !== 'all' ? `Subcategoria Selecionada: ${selectedSubcategory}` : ''}
    
Receita Total: R$ ${totalIncome.toFixed(2).replace('.', ',')}
Despesa Total: R$ ${totalExpense.toFixed(2).replace('.', ',')}
    
Despesas por Pagador:
Lais: R$ ${payerExpenses['Lais'].toFixed(2).replace('.', ',')}
André: R$ ${payerExpenses['André'].toFixed(2).replace('.', ',')}
Lais/André: R$ ${payerExpenses['Lais/André'].toFixed(2).replace('.', ',')}
    
Transações:
${transactionList.join('\n')}
        `;
    
        // Usa um campo de texto temporário para copiar o conteúdo para a área de transferência
        const tempTextArea = document.createElement('textarea');
        tempTextArea.value = summaryText.trim();
        document.body.appendChild(tempTextArea);
        tempTextArea.select();
        document.execCommand('copy');
        document.body.removeChild(tempTextArea);
        
        showAlert("Sucesso!", "Resumo mensal copiado para a área de transferência.");
    });
    
    
    // Event Listeners
    transactionForm.addEventListener('submit', async (e) => {
        e.preventDefault();
        
        const description = document.getElementById('description').value;
        const amount = document.getElementById('amount').value;
        const type = document.getElementById('type').value;
        const payer = document.getElementById('payer').value;
        const mainCategory = categorySelectEl.value;
        const subcategory = subcategorySelectEl.value;
        const paymentMethod = paymentMethodSelectEl.value;
        const isFixed = document.getElementById('is-fixed').checked;
        const isPaid = document.getElementById('is-paid').checked;
        const transactionDate = document.getElementById('transaction-date').value;
        const isRecurring = document.getElementById('is-recurring').checked;
        const recurringMonths = isRecurring ? parseInt(document.getElementById('recurring-months').value) : 1;
        const isInstallment = document.getElementById('is-installment').checked;
        const installmentsNumber = isInstallment ? parseInt(document.getElementById('installments-number').value) : 1;
        const totalAmount = parseFloat(amount);
        
        const installmentAmount = totalAmount;
        
        // Validação aprimorada
        const requiredFields = {
            description: "Descrição",
            amount: "Valor",
            type: "Tipo",
            payer: "Pagador",
            "category-select": "Categoria",
            "subcategory-select": "Subcategoria",
            "transaction-date": "Data da Transação"
        };

        const missingFields = [];
        for (const [id, name] of Object.entries(requiredFields)) {
            const element = document.getElementById(id);
            if (!element.value || (element.tagName === 'SELECT' && element.value === "")) {
                missingFields.push(name);
            }
        }
        
        // Verificação específica para a forma de pagamento
        if (type === 'despesa' && (!paymentMethod || paymentMethod === "")) {
            missingFields.push("Forma de Pagamento");
        }
        
        if (missingFields.length > 0) {
            showAlert("Erro", `Por favor, preencha os seguintes campos: ${missingFields.join(", ")}`);
            return;
        }

        const date = new Date(transactionDate);
        date.setMinutes(date.getMinutes() + date.getTimezoneOffset());
    
        const baseTransaction = {
            description,
            amount: installmentAmount,
            type,
            payer,
            mainCategory,
            subcategory,
            paymentMethod: type === 'receita' ? null : paymentMethod,
            isFixed,
            isPaid,
            userId,
        };
    
        try {
            let currentDate = new Date(date);
            for (let i = 0; i < installmentsNumber; i++) {
                let installmentDescription = description;
                if (isInstallment && installmentsNumber > 1) {
                    installmentDescription = `${description} (Parc. ${i + 1}/${installmentsNumber})`;
                }
                
                await addDoc(transactionsRef, {
                    ...baseTransaction,
                    description: installmentDescription,
                    date: new Date(currentDate),
                });
                currentDate.setMonth(currentDate.getMonth() + 1);
            }
    
            if (isRecurring && recurringMonths > 1) {
                let currentDate = new Date(date);
                for (let i = 1; i < recurringMonths; i++) {
                    currentDate.setMonth(currentDate.getMonth() + 1);
                    await addDoc(transactionsRef, {
                        ...baseTransaction,
                        date: new Date(currentDate),
                    });
                }
            }
            
            // Lógica de desconto de receitas para o pagador Lais/André
            if (isPaid && type === 'despesa' && payer === 'Lais/André') {
                if (paymentMethod === 'Cartão de Débito - Green' || paymentMethod === 'Cartão de Crédito - Green') {
                    await updateSpecificIncomeBalance('GREEN', -totalAmount);
                } else if (paymentMethod === 'Cartão de Crédito - Flash') {
                    await updateSpecificIncomeBalance('FLASH', -totalAmount);
                } else {
                    await updateSpecificIncomeBalance('APORTE LAIS', -totalAmount / 2);
                    await updateSpecificIncomeBalance('APORTE ANDRÉ', -totalAmount / 2);
                }
            } else if (type === 'receita' && mainCategory === 'Receitas Específicas') {
                 // Adiciona à receita específica
                if (subcategory === 'APORTE LAIS') {
                    await updateSpecificIncomeBalance('APORTE LAIS', totalAmount);
                } else if (subcategory === 'APORTE ANDRÉ') {
                    await updateSpecificIncomeBalance('APORTE ANDRÉ', totalAmount);
                } else if (subcategory === 'GREEN') {
                    await updateSpecificIncomeBalance('GREEN', totalAmount);
                } else if (subcategory === 'FLASH') {
                    await updateSpecificIncomeBalance('FLASH', totalAmount);
                }
            }
    
            transactionForm.reset();
            subcategoryContainer.classList.add('hidden');
            recurringMonthsGroup.classList.add('hidden');
            installmentsGroup.classList.add('hidden');
    
        } catch (error) {
            console.error("Erro ao adicionar transação:", error);
            showAlert("Erro", "Não foi possível adicionar a transação. Tente novamente.");
        }
    });
    
    editForm.addEventListener('submit', async (e) => {
        e.preventDefault();
        
        const updatedData = {
            description: editDescription.value,
            amount: parseFloat(editAmount.value),
            type: editType.value,
            payer: editPayer.value,
            mainCategory: editCategorySelectEl.value,
            subcategory: editSubcategorySelectEl.value,
            paymentMethod: editPaymentMethodSelectEl.value || null,
            isFixed: editIsFixed.checked,
            isPaid: editIsPaid.checked,
            date: new Date(editTransactionDate.value)
        };

        const originalTransaction = transactions.find(t => t.id === currentTransactionId);
        
        try {
            if (originalTransaction.isPaid !== updatedData.isPaid) {
                if (updatedData.payer === 'Lais/André' && updatedData.type === 'despesa') {
                    const amount = updatedData.amount;
                    if (updatedData.isPaid) {
                        // Pagou a despesa, descontar
                        if (updatedData.paymentMethod === 'Cartão de Débito - Green' || updatedData.paymentMethod === 'Cartão de Crédito - Green') {
                            await updateSpecificIncomeBalance('GREEN', -amount);
                        } else if (updatedData.paymentMethod === 'Cartão de Crédito - Flash') {
                            await updateSpecificIncomeBalance('FLASH', -amount);
                        } else {
                            await updateSpecificIncomeBalance('APORTE LAIS', -amount / 2);
                            await updateSpecificIncomeBalance('APORTE ANDRÉ', -amount / 2);
                        }
                    } else {
                        // Desmarcou como pago, adicionar de volta
                        if (updatedData.paymentMethod === 'Cartão de Débito - Green' || updatedData.paymentMethod === 'Cartão de Crédito - Green') {
                            await updateSpecificIncomeBalance('GREEN', amount);
                        } else if (updatedData.paymentMethod === 'Cartão de Crédito - Flash') {
                            await updateSpecificIncomeBalance('FLASH', amount);
                        } else {
                            await updateSpecificIncomeBalance('APORTE LAIS', amount / 2);
                            await updateSpecificIncomeBalance('APORTE ANDRÉ', amount / 2);
                        }
                    }
                }
            } else if (originalTransaction.type !== updatedData.type || originalTransaction.mainCategory !== updatedData.mainCategory || originalTransaction.subcategory !== updatedData.subcategory || originalTransaction.amount !== updatedData.amount) {
                // Se a transação original era uma receita específica, reverta a alteração
                if (originalTransaction.type === 'receita' && originalTransaction.mainCategory === 'Receitas Específicas') {
                    const originalAmount = originalTransaction.amount;
                    if (originalTransaction.subcategory === 'APORTE LAIS') {
                        await updateSpecificIncomeBalance('APORTE LAIS', -originalAmount);
                    } else if (originalTransaction.subcategory === 'APORTE ANDRÉ') {
                        await updateSpecificIncomeBalance('APORTE ANDRÉ', -originalAmount);
                    } else if (originalTransaction.subcategory === 'GREEN') {
                        await updateSpecificIncomeBalance('GREEN', -originalAmount);
                    } else if (originalTransaction.subcategory === 'FLASH') {
                        await updateSpecificIncomeBalance('FLASH', -originalAmount);
                    }
                }
                // Se a nova transação é uma receita específica, aplique a alteração
                if (updatedData.type === 'receita' && updatedData.mainCategory === 'Receitas Específicas') {
                    const updatedAmount = updatedData.amount;
                    if (updatedData.subcategory === 'APORTE LAIS') {
                        await updateSpecificIncomeBalance('APORTE LAIS', updatedAmount);
                    } else if (updatedData.subcategory === 'APORTE ANDRÉ') {
                        await updateSpecificIncomeBalance('APORTE ANDRÉ', updatedAmount);
                    } else if (updatedData.subcategory === 'GREEN') {
                        await updateSpecificIncomeBalance('GREEN', updatedAmount);
                    } else if (updatedData.subcategory === 'FLASH') {
                        await updateSpecificIncomeBalance('FLASH', updatedAmount);
                    }
                }
            }

            const transactionDocRef = doc(transactionsRef, currentTransactionId);
            await updateDoc(transactionDocRef, updatedData);
            editModal.classList.add('hidden');
            showAlert("Sucesso", "Transação atualizada com sucesso!");
        } catch (error) {
            console.error("Erro ao atualizar transação:", error);
            showAlert("Erro", "Não foi possível atualizar a transação. Tente novamente.");
        }
    });
    
    addCategoryForm.addEventListener('submit', async (e) => {
        e.preventDefault();
        const newCategoryName = newCategoryInput.value.trim();
        if (newCategoryName) {
            try {
                await addDoc(categoriesRef, {
                    name: newCategoryName,
                    userId: userId
                });
                newCategoryInput.value = '';
            } catch (error) {
                console.error("Erro ao adicionar categoria:", error);
                showAlert("Erro", "Não foi possível adicionar a categoria.");
            }
        }
    });

    addSubcategoryForm.addEventListener('submit', async (e) => {
        e.preventDefault();
        const newSubcategoryName = newSubcategoryInput.value.trim();
        const mainCategory = parentCategorySelect.value;
        if (newSubcategoryName && mainCategory) {
            try {
                await addDoc(subcategoriesRef, {
                    name: newSubcategoryName,
                    mainCategory: mainCategory,
                    userId: userId
                });
                newSubcategoryInput.value = '';
            } catch (error) {
                console.error("Erro ao adicionar subcategoria:", error);
                showAlert("Erro", "Não foi possível adicionar a subcategoria.");
            }
        }
    });
    
    window.deleteTransaction = async (id) => {
        try {
            const transactionDocRef = doc(transactionsRef, id);
            await deleteDoc(transactionDocRef);
        } catch (error) {
            console.error("Erro ao deletar transação:", error);
            showAlert("Erro", "Não foi possível deletar a transação. Tente novamente.");
        }
    };
    
    prevMonthBtn.addEventListener('click', () => {
        currentMonth--;
        if (currentMonth < 0) {
            currentMonth = 11;
            currentYear--;
        }
        updateDateDisplay();
        renderMonthlySummary();
    });
    
    nextMonthBtn.addEventListener('click', () => {
        currentMonth++;
        if (currentMonth > 11) {
            currentMonth = 0;
            currentYear++;
        }
        updateDateDisplay();
        renderMonthlySummary();
    });
    
    prevYearBtn.addEventListener('click', () => {
        currentYear--;
        updateDateDisplay();
        renderAnnualSummary();
    });
    
    nextYearBtn.addEventListener('click', () => {
        currentYear++;
        updateDateDisplay();
        renderAnnualSummary();
    });

    filterPayerSelect.addEventListener('change', () => {
        renderTransactionList();
    });

    filterSubcategorySelect.addEventListener('change', () => {
        renderTransactionList();
    });

    filterCategorySelect.addEventListener('change', () => {
        populateFilterSubcategoryDropdown(filterCategorySelect.value);
        renderTransactionList();
    });

    // Funções para a caixinha de dinheiro guardado
    addToSavingsBtn.addEventListener('click', async () => {
        const amount = parseFloat(savingsAmountInput.value);
        const payer = savingsPayerSelect.value;
        const currentDate = new Date();

        if (isNaN(amount) || amount <= 0) {
            showAlert("Erro", "Por favor, insira um valor válido.");
            return;
        }

        try {
            // Adiciona a transação como despesa
            await addDoc(transactionsRef, {
                description: 'Dinheiro Guardado',
                amount: amount,
                type: 'despesa',
                payer: payer,
                mainCategory: 'Caixinha de Dinheiro Guardado',
                subcategory: 'Entrada na Caixinha',
                paymentMethod: 'Dinheiro', // Exemplo
                isFixed: false,
                isPaid: true,
                date: currentDate,
                userId: userId,
            });

            // Atualiza o saldo guardado
            const savingsDocs = await getDocs(savingsRef);
            let savingsDocRef;
            if (savingsDocs.empty) {
                savingsDocRef = doc(db, 'artifacts', appId, 'public', 'data', 'savings', 'balance');
                await setDoc(savingsDocRef, { amount: amount });
            } else {
                savingsDocRef = savingsDocs.docs[0].ref;
                const currentBalance = savingsDocs.docs[0].data().amount;
                await updateDoc(savingsDocRef, { amount: currentBalance + amount });
            }
            
            // Adiciona transação ao extrato da caixinha
            await addDoc(savingsTransactionsRef, {
                amount: amount,
                type: 'entrada',
                description: 'Dinheiro Guardado',
                date: currentDate,
                userId: userId
            });

            showAlert("Sucesso", "Valor adicionado à caixinha!");
            savingsAmountInput.value = '';

        } catch (error) {
            console.error("Erro ao guardar dinheiro:", error);
            showAlert("Erro", "Não foi possível guardar o dinheiro. Tente novamente.");
        }
    });

    withdrawFromSavingsBtn.addEventListener('click', async () => {
        const amount = parseFloat(savingsAmountInput.value);
        const payer = savingsPayerSelect.value;
        const currentDate = new Date();
    
        if (isNaN(amount) || amount <= 0) {
            showAlert("Erro", "Por favor, insira um valor válido.");
            return;
        }
    
        const savingsDocs = await getDocs(savingsRef);
        if (savingsDocs.empty) {
            showAlert("Erro", "Não há saldo na caixinha para retirar.");
            return;
        }
    
        const savingsDocRef = savingsDocs.docs[0].ref;
        const currentBalance = savingsDocs.docs[0].data().amount;
    
        if (amount > currentBalance) {
            showAlert("Erro", "Valor de retirada maior que o saldo disponível.");
            return;
        }
    
        try {
            // Adiciona a transação como receita
            await addDoc(transactionsRef, {
                description: 'Resgate de Dinheiro Guardado',
                amount: amount,
                type: 'receita',
                payer: payer,
                mainCategory: 'Caixinha de Dinheiro Guardado',
                subcategory: 'Saída da Caixinha',
                paymentMethod: 'Dinheiro', // Exemplo
                isFixed: false,
                isPaid: true,
                date: currentDate,
                userId: userId,
            });
    
            // Atualiza o saldo guardado
            await updateDoc(savingsDocRef, { amount: currentBalance - amount });

            // Adiciona transação ao extrato da caixinha
            await addDoc(savingsTransactionsRef, {
                amount: amount,
                type: 'saída',
                description: 'Retirada da Caixinha',
                date: currentDate,
                userId: userId
            });
    
            showAlert("Sucesso", "Valor retirado da caixinha com sucesso!");
            savingsAmountInput.value = '';
    
        } catch (error) {
            console.error("Erro ao retirar dinheiro:", error);
            showAlert("Erro", "Não foi possível retirar o dinheiro. Tente novamente.");
        }
    });

    addIncomeToSavingsBtn.addEventListener('click', async () => {
        const amount = parseFloat(incomeAmountInput.value);
        if (isNaN(amount) || amount <= 0) {
            showAlert("Erro", "Por favor, insira um valor válido.");
            return;
        }

        try {
            const savingsDocs = await getDocs(savingsRef);
            let savingsDocRef;
            if (savingsDocs.empty) {
                savingsDocRef = doc(db, 'artifacts', appId, 'public', 'data', 'savings', 'balance');
                await setDoc(savingsDocRef, { amount: amount });
            } else {
                savingsDocRef = savingsDocs.docs[0].ref;
                const currentBalance = savingsDocs.docs[0].data().amount;
                await updateDoc(savingsDocRef, { amount: currentBalance + amount });
            }

            // Adiciona transação ao extrato da caixinha
            await addDoc(savingsTransactionsRef, {
                amount: amount,
                type: 'rendimento',
                description: 'Rendimento Adicionado',
                date: new Date(),
                userId: userId
            });

            showAlert("Sucesso", "Rendimento adicionado com sucesso!");
            incomeAmountInput.value = '';

        } catch (error) {
            console.error("Erro ao adicionar rendimento:", error);
            showAlert("Erro", "Não foi possível adicionar o rendimento. Tente novamente.");
        }
    });

    function renderSpecificIncomeSummary() {
        if (!specificIncomeTotals) return; // Adiciona esta verificação
        const summaryHtml = Object.entries(specificIncomeTotals).map(([type, total]) => `
            <div class="flex justify-between">
                <span>${type}:</span>
                <span class="font-bold text-income">R$ ${total.toFixed(2).replace('.', ',')}</span>
            </div>
        `).join('');

        specificIncomeSummaryEl.innerHTML = summaryHtml;
    }

    // Event listeners para a lista de compras
    shoppingListForm.addEventListener('submit', async (e) => {
        e.preventDefault();
        const item = shoppingItemInput.value.trim();
        const value = parseFloat(shoppingValueInput.value);
        const quantity = parseFloat(shoppingQuantityInput.value);
        const unit = shoppingUnitSelect.value;
        
        if (item) {
            try {
                await addDoc(shoppingListRef, {
                    name: item,
                    value: isNaN(value) ? null : value,
                    quantity: isNaN(quantity) ? 1 : quantity,
                    unit: unit,
                    isBought: false,
                    userId: userId,
                    createdAt: new Date(),
                });
                shoppingItemInput.value = '';
                shoppingValueInput.value = '';
                shoppingQuantityInput.value = '1';
                shoppingUnitSelect.value = 'unidade';
            } catch (error) {
                console.error("Erro ao adicionar item da lista de compras:", error);
                showAlert("Erro", "Não foi possível adicionar o item à lista.");
            }
        }
    });
    
    const tabButtons = document.querySelectorAll('.tab-button');
    const tabContents = document.querySelectorAll('.tab-content');

    tabButtons.forEach(button => {
        button.addEventListener('click', () => {
            tabButtons.forEach(btn => btn.classList.remove('active'));
            tabContents.forEach(content => content.classList.remove('active'));
            
            button.classList.add('active');
            const targetTab = document.getElementById(button.id.replace('tab-', '') + '-tab');
            if(targetTab) {
                targetTab.classList.add('active');
                if(button.id === 'tab-resumos') {
                    renderMonthlySummary();
                    renderAnnualSummary();
                }
            }
        });
    });


    // Início da aplicação
    async function init() {
        try {
            if (initialAuthToken) {
                const userCredential = await signInWithCustomToken(auth, initialAuthToken);
                userId = userCredential.user.uid;
            } else {
                const userCredential = await signInAnonymously(auth);
                userId = userCredential.user.uid;
            }
    
            // Define a referência da coleção como pública e colaborativa
            transactionsRef = collection(db, 'artifacts', appId, 'public', 'data', 'transactions');
            categoriesRef = collection(db, 'artifacts', appId, 'public', 'data', 'categories');
            subcategoriesRef = collection(db, 'artifacts', appId, 'public', 'data', 'subcategories');
            savingsRef = collection(db, 'artifacts', appId, 'public', 'data', 'savings');
            savingsTransactionsRef = collection(db, 'artifacts', appId, 'public', 'data', 'savings_transactions');
            specificIncomesRef = collection(db, 'artifacts', appId, 'public', 'data', 'specific_incomes');
            shoppingListRef = collection(db, 'artifacts', appId, 'public', 'data', 'shopping_list');

            // Garante que as categorias de receitas específicas existam no Firestore
            const specificIncomeTypes = ['FLASH', 'GREEN', 'APORTE LAIS', 'APORTE ANDRÉ'];
            const incomeDocs = await getDocs(specificIncomesRef);
            const existingIncomeTypes = incomeDocs.docs.map(doc => doc.id);
            for (const type of specificIncomeTypes) {
                if (!existingIncomeTypes.includes(type)) {
                    await setDoc(doc(specificIncomesRef, type), { type, amount: 0 });
                }
            }
    
            // Garante que a categoria "Caixinha de Dinheiro Guardado" exista
            const categoryQuery = query(categoriesRef, where('name', '==', 'Caixinha de Dinheiro Guardado'));
            const categorySnapshot = await getDocs(categoryQuery);
            let savingsCategoryId = null;

            if (categorySnapshot.empty) {
                const newCatRef = await addDoc(categoriesRef, {
                    name: 'Caixinha de Dinheiro Guardado',
                    userId: userId
                });
                savingsCategoryId = newCatRef.id;
            } else {
                savingsCategoryId = categorySnapshot.docs[0].id;
            }

            // Garante que as subcategorias existam
            const subcategoriesToAdd = ['Entrada na Caixinha', 'Saída da Caixinha'];
            for (const subcat of subcategoriesToAdd) {
                const subcatQuery = query(subcategoriesRef, where('name', '==', subcat));
                const subcatSnapshot = await getDocs(subcatQuery);
                if (subcatSnapshot.empty) {
                    await addDoc(subcategoriesRef, {
                        name: subcat,
                        mainCategory: 'Caixinha de Dinheiro Guardado',
                        userId: userId
                    });
                }
            }

            // Garante que a categoria "Receitas Específicas" exista
            const specificIncomeCategoryQuery = query(categoriesRef, where('name', '==', 'Receitas Específicas'));
            const specificIncomeCategorySnapshot = await getDocs(specificIncomeCategoryQuery);
            if (specificIncomeCategorySnapshot.empty) {
                await addDoc(categoriesRef, {
                    name: 'Receitas Específicas',
                    userId: userId
                });
            }

            // Garante que as subcategorias específicas existam
            const specificSubcategoriesToAdd = ['FLASH', 'GREEN', 'APORTE LAIS', 'APORTE ANDRÉ'];
            for (const subcat of specificSubcategoriesToAdd) {
                const subcatQuery = query(subcategoriesRef, where('name', '==', subcat));
                const subcatSnapshot = await getDocs(subcatQuery);
                if (subcatSnapshot.empty) {
                    await addDoc(subcategoriesRef, {
                        name: subcat,
                        mainCategory: 'Receitas Específicas',
                        userId: userId
                    });
                }
            }

            // Garante que a categoria "Compras" exista
            const shoppingCategoryQuery = query(categoriesRef, where('name', '==', 'Compras'));
            const shoppingCategorySnapshot = await getDocs(shoppingCategoryQuery);
            if (shoppingCategorySnapshot.empty) {
                await addDoc(categoriesRef, {
                    name: 'Compras',
                    userId: userId
                });
            }

            // Garante que a subcategoria "Itens da Lista" exista
            const shoppingSubcategoryQuery = query(subcategoriesRef, where('name', '==', 'Itens da Lista'));
            const shoppingSubcategorySnapshot = await getDocs(shoppingSubcategoryQuery);
            if (shoppingSubcategorySnapshot.empty) {
                await addDoc(subcategoriesRef, {
                    name: 'Itens da Lista',
                    mainCategory: 'Compras',
                    userId: userId
                });
            }
    
            updateDateDisplay();
            fetchData();
        } catch (error) {
            console.error("Erro na inicialização do Firebase:", error);
            showAlert("Erro de Inicialização", "Não foi possível conectar ao banco de dados. Por favor, tente recarregar a página.");
        }
    }
    
    init();
</script>
</body>
</html>

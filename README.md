
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Diário Pro - Gestão de Obras</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    
    <style>
        /* --- DESIGNER E CSS ORIGINAIS --- */
        :root {
            --primary: #475569;
            --secondary: #f8fafc;
            --accent: #3b82f6;
            --bg: #f1f5f9;
            --text-main: #1e293b;
            --text-muted: #64748b;
            --white: #ffffff;
            --success: #10b981;
            --danger: #ef4444;
            --border: #e2e8f0;
            --radius: 12px;
            --shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        }

        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: #f1f1f1; }
        ::-webkit-scrollbar-thumb { background: var(--primary); border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: var(--accent); }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Inter', system-ui, sans-serif; }
        body { background-color: var(--bg); color: var(--text-main); height: 100vh; width: 100vw; overflow: hidden; display: flex; }

        /* Login */
        #login-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: radial-gradient(circle at top left, #f1f5f9, #e2e8f0);
            display: flex; justify-content: center; align-items: center; z-index: 2000;
        }
        .login-box {
            background: var(--white); padding: 2.5rem; border-radius: var(--radius);
            box-shadow: 0 20px 25px -5px rgba(0,0,0,0.1); width: 100%; max-width: 400px;
        }

        /* Responsividade Mobile */
        #app-container { display: none; width: 100%; height: 100%; }
        
        @media (max-width: 768px) {
            body { flex-direction: column; overflow-y: auto; height: auto; }
            .sidebar { width: 100% !important; height: auto !important; border-right: none !important; border-bottom: 1px solid var(--border); padding: 15px !important; }
            .main-content { padding: 20px !important; overflow-y: visible !important; }
            .form-grid { grid-template-columns: 1fr !important; }
            .full-width { grid-column: span 1 !important; }
            table { display: block; overflow-x: auto; white-space: nowrap; }
        }

        .sidebar {
            width: 260px; background: var(--white); border-right: 1px solid var(--border);
            display: flex; flex-direction: column; padding: 24px; flex-shrink: 0;
            overflow-y: auto;
        }
        .sidebar h2 { font-size: 1.25rem; color: var(--accent); margin-bottom: 1.5rem; display: flex; align-items: center; gap: 10px; }
        
        /* CSS PARA AS ABAS DO MENU */
        .sidebar-tabs {
            display: flex; gap: 5px; margin-bottom: 15px; border-bottom: 1px solid var(--border); padding-bottom: 10px; flex-wrap: wrap;
        }
        .sidebar-tab-btn {
            flex: 1; padding: 8px 4px; text-align: center; cursor: pointer; border-radius: 6px; 
            font-size: 1.2rem; color: var(--text-muted); transition: 0.2s; background: var(--secondary);
            border: 1px solid transparent; min-width: 40px;
        }
        .sidebar-tab-btn.active { background: var(--accent); color: white; box-shadow: var(--shadow); }
        .sidebar-tab-btn:hover:not(.active) { background: #e2e8f0; }
        
        .menu-group { display: none; animation: fadeIn 0.3s ease; }
        .menu-group.active { display: block; }

        .menu-item {
            padding: 12px 16px; margin-bottom: 4px; cursor: pointer; border-radius: 8px;
            display: flex; align-items: center; color: var(--text-muted); transition: 0.2s; font-weight: 500;
        }
        .menu-item i { width: 24px; }
        .menu-item:hover, .menu-item.active { background: #eff6ff; color: var(--accent); }
        .logout-btn { margin-top: auto; color: var(--danger); border: 1px solid #fee2e2; }

        /* Estilo para Submenu */
        .submenu-container { display: none; animation: fadeIn 0.3s ease; }
        .submenu-item {
            padding: 10px 16px 10px 40px; /* Indentação */
            margin-bottom: 2px;
            cursor: pointer;
            border-radius: 8px;
            display: flex;
            align-items: center;
            color: var(--text-muted);
            font-size: 0.9rem;
            background-color: #f8fafc;
            border-left: 3px solid transparent;
        }
        .submenu-item:hover { background-color: #e2e8f0; color: var(--accent); border-left: 3px solid var(--accent); }
        .submenu-item.active { background-color: #cbd5e1; color: var(--text-main); border-left: 3px solid var(--accent); }

        .main-content { flex: 1; padding: 40px; overflow-y: auto; }
        .section { display: none; max-width: 1000px; margin: 0 auto; animation: fadeIn 0.4s ease; }
        .section.active { display: block; }

        .card { background: var(--white); padding: 25px; border-radius: var(--radius); box-shadow: var(--shadow); border: 1px solid var(--border); margin-bottom: 20px; }
        .form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        .form-group label { display: block; margin-bottom: 5px; font-weight: 600; font-size: 0.85rem; }
        input, select, textarea { width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: 8px; outline-color: var(--accent); }
        .full-width { grid-column: span 2; }

        .equip-container {
            max-height: 300px; overflow-y: auto; border: 1px solid var(--border);
            padding: 15px; border-radius: 8px; background: #fafafa;
        }
        .equip-group-title { font-weight: bold; font-size: 0.85rem; color: var(--accent); border-bottom: 1px solid #ddd; margin-top: 10px; margin-bottom: 8px; display: block; padding-bottom: 5px; }
        .equip-item { display: flex; align-items: center; gap: 8px; font-size: 0.85rem; margin-bottom: 4px; cursor: pointer; }
        .equip-item input { width: auto; }

        .photo-preview { display: flex; gap: 10px; flex-wrap: wrap; margin-top: 10px; }
        .signature-pad { border: 1px solid var(--border); background: #fafafa; border-radius: 8px; width: 100%; height: 150px; cursor: crosshair; touch-action: none; }
        
        .btn {
            background: var(--accent); color: white; border: none; padding: 10px 20px;
            border-radius: 8px; font-weight: 600; cursor: pointer; transition: 0.2s;
        }
        .btn-danger { background: var(--danger); }
        .btn-warning { background: #f59e0b; }
        .btn-success { background: var(--success); }
        
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        th { text-align: left; padding: 12px; background: var(--secondary); color: var(--text-muted); font-size: 0.8rem; }
        td { padding: 12px; border-bottom: 1px solid var(--border); font-size: 0.9rem; }

        #print-modal {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.8); z-index: 3000; overflow-y: auto; padding: 20px;
        }
        .modal-content { background: white; max-width: 900px; margin: 0 auto; padding: 40px; border-radius: 8px; }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body onload="init()">

    <div id="login-screen">
        <form class="login-box" onsubmit="event.preventDefault(); attemptLogin();">
            <h2 style="text-align:center; color: var(--primary);">🏗️ Diário Pro</h2>
            <p style="text-align:center; color: var(--text-muted); margin-bottom: 20px;">Gestão de Obras</p>
            <input type="text" id="username" placeholder="Usuário" style="margin-bottom: 10px;" autocomplete="username">
            <input type="password" id="password" placeholder="Senha" style="margin-bottom: 20px;" autocomplete="current-password">
            <button class="btn" type="submit" style="width: 100%;">Aceder ao Sistema</button>
            <p id="login-error" style="color: var(--danger); font-size: 0.8rem; text-align: center; margin-top: 10px; display: none;">Usuário ou senha incorretos.</p>
        </form>
    </div>

    <div id="app-container">
        <nav class="sidebar">
            <h2><i class="fas fa-hard-hat"></i> DiárioPro</h2>
            
            <div class="sidebar-tabs">
                <div id="tab-geral" class="sidebar-tab-btn active" onclick="switchMenu('geral')" title="Geral"><i class="fas fa-home"></i></div>
                <div id="tab-fiscal" class="sidebar-tab-btn" onclick="switchMenu('fiscal')" title="Fiscal" style="display:none;"><i class="fas fa-clipboard-list"></i></div>
                <div id="tab-engenharia" class="sidebar-tab-btn" onclick="switchMenu('engenharia')" title="Engenharia" style="display:none;"><i class="fas fa-hard-hat"></i></div>
                <div id="tab-admin" class="sidebar-tab-btn" onclick="switchMenu('admin')" title="Admin" style="display:none;"><i class="fas fa-cogs"></i></div>
            </div>

            <div id="menu-geral" class="menu-group active">
                <div class="menu-item active" onclick="showSection('dashboard')"><i class="fas fa-th-large"></i> Painel</div>
                <div id="menu-item-novo-rdo" class="menu-item" onclick="showSection('novo-rdo')"><i class="fas fa-pen-alt"></i> Novo RDO</div>
                <div id="menu-item-historico" class="menu-item" onclick="showSection('historico')"><i class="fas fa-history"></i> Histórico</div>
            </div>
            
            <div id="menu-fiscal" class="menu-group">
                <div style="margin-top: 5px; margin-bottom: 5px; font-size: 0.75rem; color: var(--text-muted); font-weight: bold; text-transform: uppercase;">Módulo Fiscal</div>
                <div class="menu-item" onclick="showSection('checklists')"><i class="fas fa-check-double"></i> Checklists Digitais</div>
                <div class="menu-item" onclick="showSection('medicoes')"><i class="fas fa-ruler-combined"></i> Medições e Relatórios</div>
                <div class="menu-item" onclick="showSection('cronograma')"><i class="fas fa-calendar-alt"></i> Controle de Cronograma</div>
                <div class="menu-item" onclick="showSection('aprovacao')"><i class="fas fa-user-check"></i> Fluxo de Aprovação</div>
                <div class="menu-item" onclick="showSection('eng-comunicacao')"><i class="fas fa-comments"></i> Comunicação</div>
            </div>

            <div id="menu-engenharia" class="menu-group">
                <div style="margin-top: 5px; margin-bottom: 5px; font-size: 0.75rem; color: var(--text-muted); font-weight: bold; text-transform: uppercase;">Módulo Engenharia</div>
                
                <div class="menu-item" onclick="toggleSubmenu('submenu-termo', 'termo-abertura')">
                    <i class="fas fa-file-invoice"></i> Termo de Abertura <i class="fas fa-caret-down" style="margin-left: auto;"></i>
                </div>
                <div id="submenu-termo" class="submenu-container">
                     <div class="submenu-item" onclick="showSection('eng-contrato')">
                        <i class="fas fa-file-contract" style="margin-right: 8px;"></i> Cadastro Contrato
                     </div>
                     <div class="submenu-item" onclick="showSection('eng-contratante')">
                        <i class="fas fa-user-tie" style="margin-right: 8px;"></i> Cadastro Contratante
                     </div>
                     <div class="submenu-item" onclick="showSection('eng-dados-obra')">
                        <i class="fas fa-building" style="margin-right: 8px;"></i> Dados da Obra/Serviço
                     </div>
                     <div class="submenu-item" onclick="showSection('eng-observacao')">
                        <i class="fas fa-edit" style="margin-right: 8px;"></i> Observação - Obra ou serviço
                     </div>
                </div>

                <div class="menu-item" onclick="showSection('eng-materiais')"><i class="fas fa-boxes"></i> Controle Materiais</div>
                <div class="menu-item" onclick="showSection('eng-execucao')"><i class="fas fa-drafting-compass"></i> Execução Técnica</div>
                <div class="menu-item" onclick="showSection('eng-qualidade')"><i class="fas fa-clipboard-check"></i> Controle Qualidade</div>
                <div class="menu-item" onclick="showSection('eng-custos')"><i class="fas fa-wallet"></i> Gestão de Custos</div>
                <div class="menu-item" onclick="showSection('eng-relatorios')"><i class="fas fa-file-invoice"></i> Relatórios Auto</div>
                <div class="menu-item" onclick="showSection('eng-comunicacao')"><i class="fas fa-comments"></i> Comunicação</div>
                <div class="menu-item" onclick="showSection('eng-seguranca')"><i class="fas fa-shield-alt"></i> Segurança (SMS)</div>
                <div class="menu-item" onclick="showSection('eng-dashboard')"><i class="fas fa-chart-line"></i> Dash Desempenho</div>
            </div>

            <div id="menu-admin" class="menu-group">
                <div style="margin-top: 5px; margin-bottom: 5px; font-size: 0.75rem; color: var(--text-muted); font-weight: bold; text-transform: uppercase;">Administração</div>
                <div class="menu-item" onclick="showSection('gestao')"><i class="fas fa-user-shield"></i> Gestão de Obras</div>
                <div class="menu-item" onclick="showSection('configuracoes')"><i class="fas fa-cog"></i> Administração do Sistema</div>
            </div>

            <div class="menu-item logout-btn" onclick="logout()"><i class="fas fa-sign-out-alt"></i> Sair</div>
        </nav>

        <main class="main-content">
            
            <div id="dashboard" class="section active">
                <h2 style="margin-bottom: 20px;">Painel de Controle</h2>
                <div class="form-grid" style="grid-template-columns: repeat(3, 1fr);">
                    <div class="card"><h3 id="dash-obras">0</h3><p style="font-size: 0.8rem;">Obras Ativas</p></div>
                    <div class="card"><h3 id="dash-rdos">0</h3><p style="font-size: 0.8rem;">RDOs Total</p></div>
                    <div class="card"><h3>100%</h3><p style="font-size: 0.8rem;">Sincronizado</p></div>
                </div>
                <div id="dash-fiscal-indicadores" class="card" style="display:none;">
                    <h4>Painel de Indicadores (Dashboard Fiscal)</h4>
                    <canvas id="chartFiscal" style="max-height: 200px; margin-top:15px;"></canvas>
                </div>
            </div>

            <div id="novo-rdo" class="section">
                <h2>Registar Atividade</h2>
                <form id="form-rdo" onsubmit="salvarRDO(event)" class="card" autocomplete="on">
                    <div class="form-grid">
                        <div class="form-group full-width"><label>Obra</label><select id="rdo-obra" required></select></div>
                        <div class="form-group"><label>Data</label><input type="date" id="rdo-data" required></div>
                        <div class="form-group"><label>Quantidade de Funcionários (Próprios)</label><input type="number" id="rdo-qtd" required></div>

                        <div class="form-group full-width">
                            <label>Equipamentos em Obra</label>
                            <div class="equip-container" id="rdo-equipamentos">
                                <span class="equip-group-title">1. Movimentação de Terra</span>
                                <label class="equip-item"><input type="checkbox" value="Escavadeira hidráulica"> Escavadeira hidráulica</label>
                                <label class="equip-item"><input type="checkbox" value="Retroescavadeira"> Retroescavadeira</label>
                                <label class="equip-item"><input type="checkbox" value="Pá carregadeira"> Pá carregadeira</label>
                                <label class="equip-item"><input type="checkbox" value="Motoniveladora"> Motoniveladora</label>
                                <label class="equip-item"><input type="checkbox" value="Trator de esteira"> Trator de esteira</label>
                                <label class="equip-item"><input type="checkbox" value="Rolo compactador"> Rolo compactador</label>
                                <label class="equip-item"><input type="checkbox" value="Caminhão basculante"> Caminhão basculante</label>
                                <label class="equip-item"><input type="checkbox" value="Mini escavadeira"> Mini escavadeira</label>
                                <label class="equip-item"><input type="checkbox" value="Betoneira"> Betoneira</label>
                                <label class="equip-item"><input type="checkbox" value="Guindaste"> Guindaste</label>
                                <label class="equip-item"><input type="checkbox" value="Serra circular"> Serra circular</label>
                                <label class="equip-item"><input type="checkbox" value="Andaimes tubulares"> Andaimes tubulares</label>
                            </div>
                        </div>

                        <div class="form-group"><label>Tempo (Manhã)</label><select id="rdo-tempo-m"><option>Sol</option><option>Nublado</option><option>Chuva</option></select></div>
                        <div class="form-group"><label>Tempo (Tarde)</label><select id="rdo-tempo-t"><option>Sol</option><option>Nublado</option><option>Chuva</option></select></div>
                        <div class="form-group full-width"><label>Tempo (Noite)</label><select id="rdo-tempo-n"><option>Sol</option><option>Nublado</option><option>Chuva</option></select></div>

                        <div class="form-group full-width"><label>Empresa Terceirizada</label><input type="text" id="rdo-terc-nome" placeholder="Nome da empresa (se houver)"></div>
                        <div class="form-group"><label>Qtd. Funcionários Terceirizada</label><input type="number" id="rdo-terc-qtd"></div>
                        <div class="form-group"><label>Serviços Executados (Terceirizada)</label><input type="text" id="rdo-terc-serv"></div>

                        <div class="form-group full-width"><label>Fotos do Dia (com Geolocalização)</label><input type="file" multiple accept="image/*" id="rdo-fotos"></div>

                        <div class="form-group full-width"><label>Relato Técnico / Observações</label><textarea id="rdo-detalhes" rows="5" required></textarea></div>
                        
                        <div class="form-group full-width"><label>Assinatura Digital</label><canvas id="signature-pad-rdo" class="signature-pad"></canvas><button type="button" class="btn" onclick="limparAssinatura('signature-pad-rdo')" style="font-size: 0.7rem; margin-top: 5px; background: var(--primary);">Limpar Assinatura</button></div>
                        
                        <div class="form-group full-width"><label>Responsável pelo Preenchimento</label><input type="text" id="rdo-resp" required></div>
                    </div>
                    
                    <button type="submit" class="btn" style="margin-top: 20px; width: 100%; display: flex; align-items: center; justify-content: center; gap: 10px;">
                        <i class="fas fa-map-marker-alt"></i> Gravar Relatório e Capturar GPS
                    </button>
                </form>
            </div>

            <div id="historico" class="section">
                <h2>Histórico de Registos</h2>
                <div class="card" style="padding: 0;"><table id="tabela-historico"><thead><tr><th>Data</th><th>Obra</th><th>Status</th><th>Ações</th></tr></thead><tbody id="corpo-historico"></tbody></table></div>
            </div>

            <div id="eng-materiais" class="section">
                <h2>Controle de Materiais</h2>
                <div class="card">
                    <div class="form-grid">
                        <div class="form-group"><label>Material / Insumo</label><input type="text" placeholder="Ex: Cimento CP-II"></div>
                        <div class="form-group"><label>Tipo de Movimento</label><select><option>Entrada</option><option>Saída / Consumo</option></select></div>
                        <div class="form-group"><label>Quantidade</label><input type="number"></div>
                        <div class="form-group"><label>Alerta de Estoque Mínimo</label><input type="number" value="10"></div>
                    </div>
                    <button class="btn btn-success" style="margin-top:15px;" onclick="registrarMovimentoMaterial()">Registrar Movimento</button>
                    <h4 style="margin-top:20px;">Estoque Atual</h4>
                    <table><thead><tr><th>Material</th><th>Estoque</th><th>Status</th></tr></thead><tbody id="lista-materiais"></tbody></table>
                </div>
            </div>

            <div id="termo-abertura" class="section">
                <h2>Termo de Abertura do Projeto (TAP)</h2>
                <div class="card">
                    <div class="form-grid">
                        <div class="form-group full-width"><label>Nome do Projeto</label><input type="text" placeholder="Digite o nome do projeto"></div>
                        <div class="form-group"><label>Gerente do Projeto</label><input type="text" placeholder="Nome do responsável"></div>
                        <div class="form-group"><label>Patrocinador (Sponsor)</label><input type="text" placeholder="Quem financia/aprova"></div>
                        <div class="form-group full-width"><label>Justificativa do Projeto</label><textarea rows="3" placeholder="Por que este projeto está sendo criado?"></textarea></div>
                        <div class="form-group full-width"><label>Objetivos do Projeto</label><textarea rows="3" placeholder="O que deve ser alcançado?"></textarea></div>
                        <div class="form-group full-width"><label>Premissas e Restrições</label><textarea rows="3"></textarea></div>
                        <div class="form-group full-width"><label>Principais Stakeholders</label><textarea rows="2" placeholder="Partes interessadas"></textarea></div>
                        <div class="form-group"><label>Estimativa de Orçamento (R$)</label><input type="number" step="0.01"></div>
                        <div class="form-group"><label>Estimativa de Prazo (Meses)</label><input type="number"></div>
                    </div>
                    <button class="btn btn-success" style="margin-top:20px;" onclick="alert('Termo de Abertura Salvo (Simulação)!')"><i class="fas fa-save"></i> Salvar Termo</button>
                </div>
            </div>

            <div id="eng-contrato" class="section">
                <h2>Cadastro de Contrato Profissional</h2>
                <div class="card">
                    <div class="form-grid">
                        <div class="form-group full-width"><label>NOME DO PROFISSIONAL</label><input type="text" id="contrato-nome" placeholder="Nome completo"></div>
                        <div class="form-group"><label>TÍTULO</label><input type="text" id="contrato-titulo" placeholder="Ex: Engenheiro Civil"></div>
                        <div class="form-group"><label>Nº DO CREA-UF \ VISTO</label><input type="text" id="contrato-crea" placeholder="000000000-0"></div>
                        <div class="form-group"><label>Nº DO RG</label><input type="text" id="contrato-rg" placeholder="00.000.000-0"></div>
                        <div class="form-group"><label>Nº DO CPF</label><input type="text" id="contrato-cpf" placeholder="000.000.000-00"></div>
                        <div class="form-group full-width"><label>ENDEREÇO</label><input type="text" id="contrato-endereco" placeholder="Rua, número, complemento"></div>
                        <div class="form-group"><label>BAIRRO</label><input type="text" id="contrato-bairro"></div>
                        <div class="form-group"><label>CIDADE</label><input type="text" id="contrato-cidade"></div>
                        <div class="form-group"><label>UF</label><input type="text" id="contrato-uf" maxlength="2" placeholder="Ex: SP"></div>
                        <div class="form-group"><label>CEP</label><input type="text" id="contrato-cep" placeholder="00000-000"></div>
                        <div class="form-group"><label>FONE</label><input type="text" id="contrato-fone" placeholder="(00) 0000-0000"></div>
                        <div class="form-group"><label>CELULAR</label><input type="text" id="contrato-celular" placeholder="(00) 90000-0000"></div>
                        <div class="form-group full-width"><label>E-MAIL</label><input type="email" id="contrato-email" placeholder="exemplo@email.com"></div>
                    </div>
                    <button class="btn btn-success" style="margin-top:20px;" onclick="alert('Contrato Cadastrado com Sucesso!')"><i class="fas fa-save"></i> Salvar Contrato</button>
                </div>
            </div>

            <div id="eng-contratante" class="section">
                <h2>Cadastro do Contratante</h2>
                <div class="card">
                    <div class="form-grid">
                        <div class="form-group full-width"><label>NOME DO CONTRATANTE</label><input type="text" id="contratante-nome" placeholder="Nome ou Razão Social"></div>
                        <div class="form-group"><label>Nº DO RG/IE</label><input type="text" id="contratante-rg-ie" placeholder="RG ou Inscrição Estadual"></div>
                        <div class="form-group"><label>Nº DO CPF/CNPJ</label><input type="text" id="contratante-cpf-cnpj" placeholder="CPF ou CNPJ"></div>
                        <div class="form-group full-width"><label>ENDEREÇO</label><input type="text" id="contratante-endereco" placeholder="Rua, número, complemento"></div>
                        <div class="form-group"><label>BAIRRO</label><input type="text" id="contratante-bairro"></div>
                        <div class="form-group"><label>CIDADE</label><input type="text" id="contratante-cidade"></div>
                        <div class="form-group"><label>UF</label><input type="text" id="contratante-uf" maxlength="2"></div>
                        <div class="form-group"><label>CEP</label><input type="text" id="contratante-cep"></div>
                        <div class="form-group"><label>FONE</label><input type="text" id="contratante-fone"></div>
                        <div class="form-group"><label>CELULAR</label><input type="text" id="contratante-celular"></div>
                        <div class="form-group full-width"><label>E-MAIL</label><input type="email" id="contratante-email"></div>
                    </div>
                    <button class="btn btn-success" style="margin-top:20px;" onclick="alert('Contratante Salvo com Sucesso!')"><i class="fas fa-save"></i> Salvar Contratante</button>
                </div>
            </div>

            <div id="eng-dados-obra" class="section">
                <h2>DADOS DA OBRA/SERVIÇO OBJETO DO CONTRATO</h2>
                <div class="card">
                    <div class="form-grid">
                        <div class="form-group"><label>BAIRRO</label><input type="text" id="obra-bairro"></div>
                        <div class="form-group"><label>CIDADE</label><input type="text" id="obra-cidade"></div>
                        <div class="form-group"><label>UF</label><input type="text" id="obra-uf" maxlength="2"></div>
                        <div class="form-group"><label>CEP</label><input type="text" id="obra-cep"></div>
                        <div class="form-group full-width"><label>NATUREZA</label><input type="text" id="obra-natureza" placeholder="Ex: Construção Civil, Reforma, Instalação"></div>
                        <div class="form-group"><label>QUANTIFICAÇÃO</label><input type="number" id="obra-quantificacao"></div>
                        <div class="form-group"><label>UNIDADE</label><input type="text" id="obra-unidade" placeholder="Ex: m², un, km"></div>
                        <div class="form-group full-width"><label>DESCRIÇÃO DOS SERVIÇOS EXECUTADOS SOB SUA RESPONSABILIDADE</label><textarea id="obra-descricao" rows="4"></textarea></div>
                        <div class="form-group"><label>DATA DO CONTRATO</label><input type="date" id="obra-data-contrato"></div>
                        <div class="form-group"><label>DATA DO INÍCIO DA EXECUÇÃO</label><input type="date" id="obra-data-inicio"></div>
                        <div class="form-group full-width"><label>DATA PROVÁVEL DA CONCLUSÃO</label><input type="date" id="obra-data-fim"></div>
                        <div class="form-group full-width"><label>LOCAL E DATA</label><input type="text" id="obra-local-data" placeholder="Cidade, Data"></div>
                        
                        <div class="form-group full-width">
                            <label>RESPONSÁVEL TÉCNICO ASS.</label>
                            <canvas id="signature-pad-resp" class="signature-pad"></canvas>
                            <button type="button" class="btn" onclick="limparAssinatura('signature-pad-resp')" style="font-size: 0.7rem; margin-top: 5px; background: var(--primary);">Limpar Assinatura</button>
                        </div>
                        
                        <div class="form-group full-width">
                            <label>CONTRATANTE ASS.</label>
                            <canvas id="signature-pad-contratante" class="signature-pad"></canvas>
                            <button type="button" class="btn" onclick="limparAssinatura('signature-pad-contratante')" style="font-size: 0.7rem; margin-top: 5px; background: var(--primary);">Limpar Assinatura</button>
                        </div>

                    </div>
                    <button class="btn btn-success" style="margin-top:20px;" onclick="alert('Dados da Obra e Assinaturas Salvos com Sucesso!')"><i class="fas fa-save"></i> Salvar Dados da Obra</button>
                </div>
            </div>

            <div id="eng-observacao" class="section">
                <h2>Observação - Obra ou Serviço</h2>
                <div class="card">
                    <div class="form-grid">
                        <div class="form-group full-width">
                            <label>Observações Gerais</label>
                            <textarea id="obra-observacao-texto" rows="12" placeholder="Descreva aqui as observações pertinentes à obra ou serviço..."></textarea>
                            <p style="font-size: 0.8rem; color: var(--danger); margin-top: 5px; font-weight: 600;">
                                Atenção, profissional: preencher de acordo com § 1º do Artigo 4º da Resolução 1024/09.
                            </p>
                        </div>
                        
                        <div class="form-group full-width">
                            <label>LOCAL E DATA</label>
                            <input type="text" id="obs-local-data" placeholder="Cidade, Data">
                        </div>
                        <div class="form-group full-width">
                            <label>RESPONSÁVEL TÉCNICO</label>
                            <input type="text" id="obs-resp-tecnico" placeholder="Nome do Responsável">
                        </div>
                        <div class="form-group full-width">
                            <label>ASSINATURA (ASS.)</label>
                            <canvas id="signature-pad-obs" class="signature-pad"></canvas>
                            <button type="button" class="btn" onclick="limparAssinatura('signature-pad-obs')" style="font-size: 0.7rem; margin-top: 5px; background: var(--primary);">Limpar Assinatura</button>
                        </div>
                    </div>
                    <button class="btn btn-success" style="margin-top:20px;" onclick="salvarObservacao()"><i class="fas fa-save"></i> Salvar Observação</button>
                </div>
            </div>

            <div id="eng-execucao" class="section">
                <h2>Execução Técnica</h2>
                <div class="card">
                    <h4>Upload de Projetos e Especificações</h4>
                    <div class="form-group full-width" style="margin-top:15px;"><input type="file" multiple></div>
                    <div style="margin-top:15px;">
                        <p style="font-size:0.9rem;"><i class="fas fa-file-pdf"></i> Projeto_Estrutural_Rev02.pdf <button class="btn" style="padding:2px 5px; font-size:0.7rem;">Baixar</button></p>
                        <p style="font-size:0.9rem; margin-top:5px;"><i class="fas fa-file-excel"></i> Memorial_Descritivo.xlsx <button class="btn" style="padding:2px 5px; font-size:0.7rem;">Baixar</button></p>
                    </div>
                </div>
                <div class="card">
                    <h4>Checklist de Execução</h4>
                    <div class="equip-container">
                        <label class="equip-item"><input type="checkbox"> Verificação de eixos e gabarito</label>
                        <label class="equip-item"><input type="checkbox"> Conferência de armadura positiva</label>
                        <label class="equip-item"><input type="checkbox"> Espaçadores instalados</label>
                    </div>
                </div>
            </div>

            <div id="eng-qualidade" class="section">
                <h2>Controle de Qualidade</h2>
                <div class="card">
                    <h4>Registro de Não Conformidade (RNC)</h4>
                    <div class="form-grid">
                        <div class="form-group full-width"><label>Descrição da Ocorrência</label><textarea rows="3"></textarea></div>
                        <div class="form-group"><label>Gravidade</label><select><option>Baixa</option><option>Média</option><option>Alta</option></select></div>
                        <div class="form-group"><label>Ação Corretiva</label><input type="text"></div>
                    </div>
                    <button class="btn btn-warning" style="margin-top:15px;" onclick="alert('RNC Registrada e Enviada ao Gestor!')">Registrar RNC</button>
                </div>
            </div>

            <div id="eng-custos" class="section">
                <h2>Gestão de Custos</h2>
                <div class="card">
                    <div class="form-grid"><div class="form-group"><label>Importar Orçamento (.csv)</label><input type="file"></div></div>
                    <table style="margin-top:15px;">
                        <thead><tr><th>Item</th><th>Orçado</th><th>Executado</th><th>Desvio</th></tr></thead>
                        <tbody>
                            <tr><td>Mão de Obra</td><td>R$ 50.000</td><td>R$ 52.000</td><td style="color:var(--danger)">+4%</td></tr>
                            <tr><td>Materiais</td><td>R$ 120.000</td><td>R$ 110.000</td><td style="color:var(--success)">-8%</td></tr>
                        </tbody>
                    </table>
                </div>
            </div>

            <div id="eng-relatorios" class="section">
                <h2>Relatórios Automáticos</h2>
                <div class="card">
                    <p style="margin-bottom:15px;">Gere relatórios consolidados com base nos RDOs e Medições.</p>
                    <div class="form-grid">
                        <div class="form-group"><label>Período Inicial</label><input type="date"></div>
                        <div class="form-group"><label>Período Final</label><input type="date"></div>
                        <div class="form-group full-width"><label>Tipo de Relatório</label>
                            <select><option>Diário Consolidado</option><option>Relatório Semanal de Avanço</option><option>Relatório Mensal de Custos</option></select>
                        </div>
                    </div>
                    <button class="btn" style="margin-top:15px;"><i class="fas fa-file-pdf"></i> Gerar Relatório PDF</button>
                </div>
            </div>

            <div id="eng-comunicacao" class="section">
                <h2>Comunicação Integrada</h2>
                <div class="card" style="display:flex; flex-direction:column; height: 400px;">
                    <div id="chat-container" style="flex:1; background:#f1f5f9; border:1px solid var(--border); border-radius:8px; padding:15px; overflow-y:auto; margin-bottom:15px;">
                        </div>
                    <div style="display:flex; gap:10px;">
                        <input type="text" placeholder="Digite sua mensagem..." style="flex:1;">
                        <button class="btn" onclick="enviarMensagem()"><i class="fas fa-paper-plane"></i></button>
                    </div>
                </div>
            </div>

            <div id="eng-seguranca" class="section">
                <h2>Segurança e Conformidade (SMS)</h2>
                <div class="card">
                    <h4>Registro de Treinamento / DDS</h4>
                    <div class="form-grid">
                        <div class="form-group"><label>Tema do Treinamento</label><input type="text" placeholder="Ex: Trabalho em Altura"></div>
                        <div class="form-group"><label>Instrutor</label><input type="text"></div>
                        <div class="form-group full-width"><label>Funcionários Presentes (Lista)</label><textarea rows="2"></textarea></div>
                    </div>
                    <button class="btn btn-success" style="margin-top:15px;" onclick="alert('Treinamento Registrado!')">Registrar Treinamento</button>
                </div>
            </div>

            <div id="eng-dashboard" class="section">
                <h2>Dashboard de Desempenho</h2>
                <div class="card">
                    <h4>Indicadores de Performance (KPIs)</h4>
                    <canvas id="chartPerformance" style="max-height: 300px; width: 100%;"></canvas>
                </div>
            </div>

            <div id="checklists" class="section">
                <h2>Checklists Digitais</h2>
                <div class="card">
                    <div class="form-group full-width"><label>Tipo de Inspeção</label><select id="checklist-tipo"><option>Inspeção de Segurança</option><option>Qualidade de Materiais</option><option>Conformidade Técnica</option></select></div>
                    <div class="equip-container" style="margin-top:15px;">
                        <label class="equip-item"><input type="checkbox"> Uso de EPI obrigatório</label>
                        <label class="equip-item"><input type="checkbox"> Sinalização de área ativa</label>
                        <label class="equip-item"><input type="checkbox"> Armazenamento correto de materiais</label>
                    </div>
                    <button class="btn btn-success" style="margin-top:15px;" onclick="salvarChecklist()">Salvar Inspeção Digital</button>
                </div>
            </div>

            <div id="medicoes" class="section">
                <h2>Medições e Relatórios Automáticos</h2>
                <div class="card">
                    <div class="form-grid">
                        <div class="form-group"><label>Item de Medição</label><input type="text" placeholder="Ex: Escavação"></div>
                        <div class="form-group"><label>Quantidade Medida</label><input type="number"></div>
                    </div>
                    <button class="btn" style="margin-top:15px;" onclick="salvarMedicao()">Registrar Medição</button>
                    <table style="margin-top:20px;">
                        <thead><tr><th>Item</th><th>Medido</th><th>Data</th></tr></thead>
                        <tbody id="lista-medicoes"></tbody>
                    </table>
                </div>
            </div>

            <div id="cronograma" class="section">
                <h2>Controle de Cronograma</h2>
                <div class="card">
                    <p>Status da Obra: <span style="color:var(--success); font-weight:bold;">No Prazo</span></p>
                    <div style="background:#eee; height:20px; border-radius:10px; margin: 15px 0;"><div style="background:var(--accent); width:65%; height:100%; border-radius:10px; text-align:center; color:white; font-size:0.7rem;">65% Concluído</div></div>
                </div>
            </div>

            <div id="aprovacao" class="section">
                <h2>Fluxo de Aprovação</h2>
                <div class="card">
                    <h4>Pendências de Assinatura</h4>
                    <table>
                        <thead><tr><th>RDO ID</th><th>Data</th><th>Origem</th><th>Status</th><th>Ação</th></tr></thead>
                        <tbody id="lista-aprovacoes"></tbody>
                    </table>
                </div>
            </div>

            <div id="gestao" class="section">
                <h2>Gestão e Controle de Acessos</h2>
                <div class="card">
                    <h4 id="titulo-obra-form"><i class="fas fa-city"></i> Cadastrar Nova Obra</h4>
                    <form onsubmit="event.preventDefault(); adicionarObra();" class="form-grid" style="margin-top: 15px;">
                        <input type="hidden" id="edit-obra-index" value="-1">
                        
                        <div class="form-group full-width"><label>Nome da Obra</label><input type="text" id="new-obra-nome" required></div>
                        
                        <div class="form-group"><label>Engenheiro Responsável</label><select id="new-obra-eng"><option value="admin">admin</option><option value="hernande">hernande</option></select></div>
                        <div class="form-group"><label>Fiscal Vinculado</label><select id="new-obra-fiscal"><option value="admin">admin</option><option value="thiago">thiago</option></select></div>
                        
                        <div class="form-group"><label>Nº Alvará</label><input type="text" id="new-obra-alvara"></div>
                        <div class="form-group"><label>Licença Ambiental</label><input type="text" id="new-obra-licenca"></div>
                        
                        <div class="form-group"><label>Valor Contratado (R$)</label><input type="number" step="0.01" id="new-obra-valor-contrato"></div>
                        <div class="form-group"><label>Valor Medido (R$)</label><input type="number" step="0.01" id="new-obra-valor-medido"></div>
                        
                        <div class="form-group full-width"><label>Prazo Término</label><input type="date" id="new-obra-prazo"></div>

                        <div class="form-group"><label>Clima Manhã</label><select id="new-obra-clima-m"><option>Sol</option><option>Nublado</option><option>Chuva</option></select></div>
                        <div class="form-group"><label>Clima Tarde</label><select id="new-obra-clima-t"><option>Sol</option><option>Nublado</option><option>Chuva</option></select></div>
                        <div class="form-group full-width"><label>Clima Noite</label><select id="new-obra-clima-n"><option>Sol</option><option>Nublado</option><option>Chuva</option></select></div>

                        <div class="form-group full-width">
                            <label>Turno / Horário de Trabalho</label>
                            <select id="new-obra-turno">
                                <option>Turno Único (07h às 17h)</option>
                                <option>Dois Turnos (06h às 22h)</option>
                                <option>Três Turnos (24 horas)</option>
                                <option>Turno Administrativo (08h às 18h)</option>
                                <option>Escala 12x36</option>
                                <option>Plantão Especial</option>
                            </select>
                        </div>
                        
                        <button class="btn" type="submit" id="btn-salvar-obra">Salvar Obra</button>
                    </form>
                </div>
                <div class="card">
                    <h4>Obras Cadastradas</h4>
                    <table style="margin-top: 10px;"><thead><tr><th>Obra</th><th>Engenheiro</th><th>Ações</th></tr></thead><tbody id="lista-obras-gestao"></tbody></table>
                </div>
            </div>

            <div id="configuracoes" class="section">
                <h2>Administração do Sistema</h2>
                <div class="card">
                    <h4>Backup e Restauração</h4>
                    <div style="display: flex; gap: 10px; flex-wrap: wrap;">
                        <button class="btn" onclick="exportarDados()"><i class="fas fa-download"></i> Exportar Backup</button>
                        <button class="btn btn-warning" onclick="importarDados()"><i class="fas fa-upload"></i> Restaurar Backup</button>
                        <button class="btn btn-danger" onclick="limparTudo()"><i class="fas fa-trash-alt"></i> Resetar Sistema</button>
                    </div>
                </div>
                <div class="card">
                    <h4><i class="fas fa-users"></i> Gestão de Usuários</h4>
                    <form onsubmit="event.preventDefault(); salvarUsuario();" class="form-grid" style="margin-top: 15px;" id="form-usuario">
                        <div class="form-group"><label>Usuário</label><input type="text" id="new-user-name"></div>
                        <div class="form-group"><label>Senha</label><input type="password" id="new-user-pass"></div>
                        <div class="form-group full-width"><label>Nível de Acesso</label>
                            <select id="new-user-level">
                                <option value="5">Administrador (Total)</option>
                                <option value="4">Empreiteiro (Gestor de Obra)</option>
                                <option value="3">Engenheiro (Operador)</option>
                                <option value="2">Gestão Fiscal</option>
                                <option value="1">Visitante (Apenas Leitura)</option>
                            </select>
                        </div>
                        <button class="btn" type="submit" id="btn-salvar-user">Salvar Usuário</button>
                        <button class="btn btn-danger" type="button" onclick="cancelarEdicaoUsuario()" style="margin-left: 10px;">Cancelar</button>
                    </form>
                    <table style="margin-top: 20px;"><thead><tr><th>Usuário</th><th>Perfil</th><th>Ações</th></tr></thead><tbody id="lista-usuarios"></tbody></table>
                </div>
            </div>
        </main>
    </div>

    <div id="print-modal"><div class="modal-content"><div id="print-content"></div><hr style="margin: 20px 0;"><button class="btn" onclick="window.print()">Imprimir</button><button class="btn btn-danger" onclick="document.getElementById('print-modal').style.display='none'">Fechar</button></div></div>

    <script>
        let currentUser = null;

        // DADOS DO BACKUP INTEGRADOS
        const backupData = {
            "usuarios": {
                "admin": {"pass": "123", "level": 5},
                "engenheiro": {"pass": "123", "level": 3},
                "empreiteiro": {"pass": "123", "level": 4},
                "fiscal": {"pass": "123", "level": 2},
                "hernande": {"pass": "123", "level": 3},
                "thiago": {"pass": "123", "level": 2},
                "lucas": {"pass": "123", "level": 1}
            },
            "obras": [
                {
                    "nome": "Moles de Itaipú",
                    "engenheiro": "hernande",
                    "fiscal": "thiago",
                    "alvara": "123/2026",
                    "licenca": "456/2026",
                    "valorContrato": "1200000",
                    "valorMedido": "111000",
                    "prazo": "2027-06-26",
                    "climaManha": "Nublado",
                    "climaTarde": "Nublado",
                    "climaNoite": "Nublado",
                    "turno": "Três Turnos (24 horas)"
                }
            ],
            "relatorios": [
                {
                    "obra": "Moles de Itaipú",
                    "data": "2026-01-26",
                    "qtd": "120",
                    "equipamentos": ["Escavadeira hidráulica", "Pá carregadeira"],
                    "clima": {"manha": "Nublado", "tarde": "Chuva", "noite": "Sol"},
                    "terceirizada": {"nome": "SH Reformas e Construções", "qtd": "85", "servico": "Civil e Elétrica"},
                    "detalhes": "Início das atividades de escavação.",
                    "responsavel": "hernande",
                    "assinatura": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAAAXNSR0IArs4c6QAAAAtJREFUGFdjYAACAAAFAAGq1cRzAAAAAElFTkSuQmCC", 
                    "timestamp": 1706295000000,
                    "status": "Pendente",
                    "gps": "-22.909938, -43.102376"
                }
            ],
            "checklists": []
        };

        function init() {
            if (!localStorage.getItem('obras')) localStorage.setItem('obras', JSON.stringify(backupData.obras));
            if (!localStorage.getItem('relatorios')) localStorage.setItem('relatorios', JSON.stringify(backupData.relatorios));
            if (!localStorage.getItem('usuarios')) localStorage.setItem('usuarios', JSON.stringify(backupData.usuarios));
            if (!localStorage.getItem('checklists')) localStorage.setItem('checklists', JSON.stringify(backupData.checklists));
            if (!localStorage.getItem('materiais')) localStorage.setItem('materiais', JSON.stringify([{nome: "Cimento CP-II", qtd: 45}, {nome: "Areia Média", qtd: 2}]));
            if (!localStorage.getItem('mensagens')) localStorage.setItem('mensagens', JSON.stringify([]));
            if (!localStorage.getItem('medicoes')) localStorage.setItem('medicoes', JSON.stringify([]));
            
            // Tenta recuperar observação salva (incluindo novos campos)
            const savedObs = JSON.parse(localStorage.getItem('obs_obra_completa'));
            if (savedObs) {
                if(document.getElementById('obra-observacao-texto')) document.getElementById('obra-observacao-texto').value = savedObs.txt || '';
                if(document.getElementById('obs-local-data')) document.getElementById('obs-local-data').value = savedObs.local || '';
                if(document.getElementById('obs-resp-tecnico')) document.getElementById('obs-resp-tecnico').value = savedObs.resp || '';
            }

            setupAllSignatures();
            renderUsuarios();
            renderObrasGestao();
            atualizarDashboard();
        }

        function switchMenu(menuName) {
            document.querySelectorAll('.menu-group').forEach(el => el.classList.remove('active'));
            document.querySelectorAll('.sidebar-tab-btn').forEach(el => el.classList.remove('active'));
            const targetMenu = document.getElementById('menu-' + menuName);
            const targetTab = document.getElementById('tab-' + menuName);
            if(targetMenu) targetMenu.classList.add('active');
            if(targetTab) targetTab.classList.add('active');
        }

        function attemptLogin() {
            const u = document.getElementById('username').value;
            const p = document.getElementById('password').value;
            const users = JSON.parse(localStorage.getItem('usuarios'));
            
            if(users[u] && users[u].pass === p) {
                currentUser = users[u];
                currentUser.name = u;
                document.getElementById('login-screen').style.display = 'none';
                document.getElementById('app-container').style.display = 'flex';
                
                document.getElementById('tab-admin').style.display = 'none';
                document.getElementById('tab-engenharia').style.display = 'none';
                document.getElementById('tab-fiscal').style.display = 'none';
                document.getElementById('menu-item-novo-rdo').style.display = 'flex';
                document.getElementById('menu-item-historico').style.display = 'flex';

                if(currentUser.level == 5) {
                    document.getElementById('tab-admin').style.display = 'block';
                    document.getElementById('tab-engenharia').style.display = 'block';
                    document.getElementById('tab-fiscal').style.display = 'block';
                    document.getElementById('dash-fiscal-indicadores').style.display = 'block';
                } else if(currentUser.level == 4) { // Empreiteiro
                    document.getElementById('tab-engenharia').style.display = 'block';
                    document.getElementById('tab-fiscal').style.display = 'block';
                    // Opcional: mostrar dashboard fiscal
                    document.getElementById('dash-fiscal-indicadores').style.display = 'block';
                } else if(currentUser.level == 3) {
                    document.getElementById('tab-engenharia').style.display = 'block';
                } else if(currentUser.level == 2) {
                    document.getElementById('tab-fiscal').style.display = 'block';
                    document.getElementById('dash-fiscal-indicadores').style.display = 'block';
                } else if(currentUser.level == 1) {
                    document.getElementById('menu-item-novo-rdo').style.display = 'none';
                    document.getElementById('menu-item-historico').style.display = 'none';
                }

                switchMenu('geral');
                carregarObras();
                renderHistorico();
                atualizarDashboard();
                if(document.getElementById('dash-fiscal-indicadores').style.display === 'block') renderChartFiscal();
            } else { document.getElementById('login-error').style.display = 'block'; }
        }

        function toggleSubmenu(submenuId, parentSectionId) {
            showSection(parentSectionId);
            const sub = document.getElementById(submenuId);
            if(sub.style.display === 'none' || sub.style.display === '') {
                sub.style.display = 'block';
            } else {
                sub.style.display = 'none';
            }
        }

        function renderChartFiscal() {
            const ctx_chart = document.getElementById('chartFiscal').getContext('2d');
            new Chart(ctx_chart, { type: 'line', data: { labels: ['Jan', 'Fev', 'Mar', 'Abr'], datasets: [{ label: 'Evolução Física %', data: [10, 25, 45, 65], borderColor: '#3b82f6', fill: false }] } });
        }

        function renderChartPerformance() {
            const ctx_perf = document.getElementById('chartPerformance').getContext('2d');
            new Chart(ctx_perf, { type: 'bar', data: { labels: ['Avanço', 'Orçado', 'Real', 'Produtividade'], datasets: [{ label: '%', data: [65, 80, 85, 90], backgroundColor: ['#3b82f6', '#10b981', '#ef4444', '#f59e0b'] }] } });
        }

        function setupAllSignatures() {
            const pads = document.querySelectorAll('.signature-pad');
            pads.forEach(p => {
                const ctx = p.getContext('2d');
                p.width = p.offsetWidth;
                p.height = p.offsetHeight;
                let drawing = false;
                const getPos = (e) => {
                    const rect = p.getBoundingClientRect();
                    return { x: (e.touches ? e.touches[0].clientX : e.clientX) - rect.left, y: (e.touches ? e.touches[0].clientY : e.clientY) - rect.top };
                };
                p.addEventListener('mousedown', (e) => { drawing = true; ctx.beginPath(); const pos = getPos(e); ctx.moveTo(pos.x, pos.y); });
                p.addEventListener('mousemove', (e) => { if(!drawing) return; const pos = getPos(e); ctx.lineTo(pos.x, pos.y); ctx.stroke(); });
                window.addEventListener('mouseup', () => { drawing = false; });
                p.addEventListener('touchstart', (e) => { e.preventDefault(); drawing = true; ctx.beginPath(); const pos = getPos(e); ctx.moveTo(pos.x, pos.y); }, {passive: false});
                p.addEventListener('touchmove', (e) => { e.preventDefault(); if(!drawing) return; const pos = getPos(e); ctx.lineTo(pos.x, pos.y); ctx.stroke(); }, {passive: false});
                p.addEventListener('touchend', () => { drawing = false; });
            });
        }

        function limparAssinatura(canvasId) {
            const c = document.getElementById(canvasId);
            if(c) { const cx = c.getContext('2d'); cx.clearRect(0, 0, c.width, c.height); }
        }

        function salvarObservacao() {
            const txt = document.getElementById('obra-observacao-texto').value;
            const local = document.getElementById('obs-local-data').value;
            const resp = document.getElementById('obs-resp-tecnico').value;
            const canvas = document.getElementById('signature-pad-obs');
            const sign = canvas.toDataURL();

            const data = { txt, local, resp, sign };
            localStorage.setItem('obs_obra_completa', JSON.stringify(data));
            alert("Observação e Assinatura salvas!");
        }

        function salvarRDO(e) {
            e.preventDefault();
            const canvasRdo = document.getElementById('signature-pad-rdo');
            
            const equipamentosSelecionados = [];
            document.querySelectorAll('#rdo-equipamentos input:checked').forEach(checkbox => {
                equipamentosSelecionados.push(checkbox.value);
            });

            const dadosRDO = {
                obra: document.getElementById('rdo-obra').value,
                data: document.getElementById('rdo-data').value,
                qtd: document.getElementById('rdo-qtd').value,
                equipamentos: equipamentosSelecionados,
                clima: {
                    manha: document.getElementById('rdo-tempo-m').value,
                    tarde: document.getElementById('rdo-tempo-t').value,
                    noite: document.getElementById('rdo-tempo-n').value,
                },
                terceirizada: {
                    nome: document.getElementById('rdo-terc-nome').value,
                    qtd: document.getElementById('rdo-terc-qtd').value,
                    servico: document.getElementById('rdo-terc-serv').value
                },
                detalhes: document.getElementById('rdo-detalhes').value,
                responsavel: document.getElementById('rdo-resp').value,
                assinatura: canvasRdo.toDataURL(),
                timestamp: new Date().getTime(),
                status: 'Pendente',
                gps: 'Aguardando...'
            };

            if("geolocation" in navigator) {
                alert("Solicitando localização GPS...");
                navigator.geolocation.getCurrentPosition(function(position) {
                    dadosRDO.gps = position.coords.latitude + ", " + position.coords.longitude;
                    finalizarSalvamento(dadosRDO);
                }, function(error) {
                    dadosRDO.gps = "Erro/Negado: " + error.message;
                    finalizarSalvamento(dadosRDO);
                });
            } else {
                dadosRDO.gps = "Não suportado";
                finalizarSalvamento(dadosRDO);
            }
        }

        function finalizarSalvamento(dados) {
            const rdos = JSON.parse(localStorage.getItem('relatorios'));
            rdos.push(dados);
            localStorage.setItem('relatorios', JSON.stringify(rdos));
            alert("RDO Gravado com Sucesso! Coordenadas: " + dados.gps); 
            location.reload();
        }

        function carregarObras() {
            const obras = JSON.parse(localStorage.getItem('obras'));
            const select = document.getElementById('rdo-obra');
            if(select) select.innerHTML = obras.map(o => `<option value="${o.nome}">${o.nome}</option>`).join('');
        }

        function adicionarObra() {
            const nome = document.getElementById('new-obra-nome').value;
            if(!nome) return alert("Insira o nome da Obra");
            const obras = JSON.parse(localStorage.getItem('obras'));
            const novaObra = { 
                nome: nome, 
                engenheiro: document.getElementById('new-obra-eng').value,
                fiscal: document.getElementById('new-obra-fiscal').value,
                alvara: document.getElementById('new-obra-alvara').value,
                licenca: document.getElementById('new-obra-licenca').value,
                valorContrato: document.getElementById('new-obra-valor-contrato').value,
                valorMedido: document.getElementById('new-obra-valor-medido').value,
                prazo: document.getElementById('new-obra-prazo').value,
                climaManha: document.getElementById('new-obra-clima-m').value,
                climaTarde: document.getElementById('new-obra-clima-t').value,
                climaNoite: document.getElementById('new-obra-clima-n').value,
                turno: document.getElementById('new-obra-turno').value
            };
            obras.push(novaObra);
            localStorage.setItem('obras', JSON.stringify(obras)); 
            location.reload();
        }

        function renderObrasGestao() {
            const obras = JSON.parse(localStorage.getItem('obras'));
            const container = document.getElementById('lista-obras-gestao');
            if(container) container.innerHTML = obras.map(o => `<tr><td>${o.nome}</td><td>${o.engenheiro}</td><td>---</td></tr>`).join('');
        }

        function renderUsuarios() {
            const users = JSON.parse(localStorage.getItem('usuarios'));
            let html = '';
            for(let u in users) {
                let perfil = "Visitante";
                if(users[u].level == 5) perfil = "Administrador";
                else if(users[u].level == 4) perfil = "Empreiteiro";
                else if(users[u].level == 3) perfil = "Engenheiro";
                else if(users[u].level == 2) perfil = "Gestão Fiscal";
                html += `<tr>
                    <td>${u}</td>
                    <td>${perfil}</td>
                    <td>
                        <button class="btn btn-warning" style="padding: 4px 8px; font-size: 0.8rem;" onclick="editarUsuario('${u}')">
                            <i class="fas fa-edit"></i>
                        </button>
                        <button class="btn btn-danger" style="padding: 4px 8px; font-size: 0.8rem; margin-left: 5px;" onclick="excluirUsuario('${u}')">
                            <i class="fas fa-trash"></i>
                        </button>
                    </td>
                </tr>`;
            }
            document.getElementById('lista-usuarios').innerHTML = html;
        }

        function editarUsuario(u) {
            const users = JSON.parse(localStorage.getItem('usuarios'));
            const user = users[u];
            document.getElementById('new-user-name').value = u;
            document.getElementById('new-user-name').readOnly = true; 
            document.getElementById('new-user-name').style.backgroundColor = "#e2e8f0"; 
            document.getElementById('new-user-pass').value = user.pass;
            document.getElementById('new-user-level').value = user.level;
            document.getElementById('btn-salvar-user').innerText = "Atualizar Senha/Nível";
        }

        function excluirUsuario(u) {
            if(u === currentUser.name) {
                alert("Você não pode excluir seu próprio usuário enquanto está logado.");
                return;
            }
            if(confirm("Tem certeza que deseja excluir o usuário " + u + "?")) {
                const users = JSON.parse(localStorage.getItem('usuarios'));
                delete users[u];
                localStorage.setItem('usuarios', JSON.stringify(users));
                renderUsuarios();
                alert("Usuário excluído.");
            }
        }
        
        function cancelarEdicaoUsuario() {
             document.getElementById('new-user-name').value = "";
             document.getElementById('new-user-name').readOnly = false;
             document.getElementById('new-user-name').style.backgroundColor = "";
             document.getElementById('new-user-pass').value = "";
             document.getElementById('new-user-level').value = "5";
             document.getElementById('btn-salvar-user').innerText = "Salvar Usuário";
        }

        function salvarUsuario() {
            const u = document.getElementById('new-user-name').value;
            const p = document.getElementById('new-user-pass').value;
            const l = document.getElementById('new-user-level').value;
            if(!u || !p) return alert("Preencha todos os campos");
            const users = JSON.parse(localStorage.getItem('usuarios'));
            users[u] = {pass: p, level: parseInt(l)};
            localStorage.setItem('usuarios', JSON.stringify(users)); 
            cancelarEdicaoUsuario();
            renderUsuarios();
            alert("Usuário salvo com sucesso!");
        }

        function renderHistorico() {
            const rdos = JSON.parse(localStorage.getItem('relatorios'));
            document.getElementById('corpo-historico').innerHTML = rdos.map((r, idx) => {
                const statusColor = r.status === 'Aprovado' ? 'green' : (r.status === 'Pendente' ? 'orange' : 'black');
                return `<tr><td>${r.data}</td><td>${r.obra}</td><td style="color:${statusColor}; font-weight:bold;">${r.status || 'N/A'}</td><td><button class="btn" onclick="abrirRDO(${idx})">Abrir</button></td></tr>`;
            }).join('');
        }

        function abrirRDO(idx) {
            const r = JSON.parse(localStorage.getItem('relatorios'))[idx];
            const equips = r.equipamentos ? r.equipamentos.join(', ') : 'Nenhum';
            const clima = r.clima ? `M:${r.clima.manha}/T:${r.clima.tarde}/N:${r.clima.noite}` : 'N/A';
            document.getElementById('print-content').innerHTML = `
                <h2>RDO: ${r.obra}</h2>
                <p>Data: ${r.data}</p>
                <p>Status: <b>${r.status || 'Pendente'}</b></p>
                <p>Responsável: ${r.responsavel}</p>
                <p>GPS: ${r.gps || 'Não capturado'}</p>
                <hr>
                <p><b>Equipamentos:</b> ${equips}</p>
                <p><b>Clima:</b> ${clima}</p>
                <p><b>Terceirizada:</b> ${r.terceirizada ? r.terceirizada.nome : ''}</p>
                <p><b>Relato:</b> ${r.detalhes}</p>
                <img src="${r.assinatura}" style="width:200px; border:1px solid #ccc;">
            `;
            document.getElementById('print-modal').style.display = 'block';
        }

        function atualizarDashboard() { 
            const obrasNum = JSON.parse(localStorage.getItem('obras')).length;
            const rdos = JSON.parse(localStorage.getItem('relatorios'));
            if(document.getElementById('dash-obras')) document.getElementById('dash-obras').innerText = obrasNum; 
            if(document.getElementById('dash-rdos')) document.getElementById('dash-rdos').innerText = rdos.length;
            const pendentes = rdos.filter(r => r.status === 'Pendente').length;
            const cardSinc = document.querySelector('#dashboard .card:nth-child(3)');
            if(cardSinc) {
                if(pendentes > 0) cardSinc.innerHTML = `<h3 style="color:orange">${pendentes}</h3><p style="font-size:0.8rem">RDOs Pendentes</p>`;
                else cardSinc.innerHTML = `<h3 style="color:var(--success)">100%</h3><p style="font-size:0.8rem">Sincronizado</p>`;
            }
        }
        
        function showSection(id) { 
            document.querySelectorAll('.section').forEach(s => s.classList.remove('active')); 
            document.getElementById(id).classList.add('active'); 
            setTimeout(setupAllSignatures, 300);
            if(id === 'eng-dashboard') setTimeout(renderChartPerformance, 300);
            if(id === 'eng-materiais') renderEstoque();
            if(id === 'eng-comunicacao') renderChat();
            if(id === 'aprovacao') renderAprovacoes();
            if(id === 'medicoes') renderMedicoes();
        }
        
        function registrarMovimentoMaterial() {
            const nome = document.querySelector('#eng-materiais input[type="text"]').value;
            const tipo = document.querySelector('#eng-materiais select').value;
            const qtd = parseFloat(document.querySelector('#eng-materiais input[type="number"]').value);
            if(!nome || !qtd) return alert("Preencha o material e a quantidade.");
            let estoque = JSON.parse(localStorage.getItem('materiais'));
            let item = estoque.find(i => i.nome === nome);
            if (!item) { item = { nome: nome, qtd: 0 }; estoque.push(item); } 
            else { item = estoque.find(i => i.nome === nome); }
            if (tipo === 'Entrada') item.qtd += qtd; else item.qtd -= qtd;
            localStorage.setItem('materiais', JSON.stringify(estoque));
            renderEstoque();
            alert("Movimento registrado!");
        }

        function renderEstoque() {
            const estoque = JSON.parse(localStorage.getItem('materiais'));
            const tbody = document.getElementById('lista-materiais');
            if(!tbody) return;
            tbody.innerHTML = estoque.map(item => {
                let status = item.qtd < 10 ? '<span style="color:var(--danger)">Baixo</span>' : '<span style="color:var(--success)">OK</span>';
                return `<tr><td>${item.nome}</td><td>${item.qtd}</td><td>${status}</td></tr>`;
            }).join('');
        }

        function enviarMensagem() {
            const input = document.querySelector('#eng-comunicacao input[type="text"]');
            const texto = input.value;
            if(!texto) return;
            const msgs = JSON.parse(localStorage.getItem('mensagens'));
            const nomeUser = currentUser ? (currentUser.name || 'Usuário') : 'Anonimo';
            msgs.push({ user: nomeUser, text: texto, time: new Date().toLocaleTimeString().slice(0,5) });
            localStorage.setItem('mensagens', JSON.stringify(msgs));
            input.value = '';
            renderChat();
        }

        function renderChat() {
            const msgs = JSON.parse(localStorage.getItem('mensagens'));
            const container = document.getElementById('chat-container');
            if(!container) return;
            container.innerHTML = msgs.map(m => `
                <div style="margin-bottom:10px;">
                    <b style="color:var(--accent);">${m.user} <small>(${m.time})</small>:</b> 
                    <span style="font-size:0.9rem;">${m.text}</span>
                </div>
            `).join('');
            container.scrollTop = container.scrollHeight;
        }

        function renderAprovacoes() {
            const rdos = JSON.parse(localStorage.getItem('relatorios'));
            const tbody = document.getElementById('lista-aprovacoes');
            if(!tbody) return;
            const pendentes = rdos.map((r, idx) => ({...r, originalIdx: idx})).filter(r => r.status === 'Pendente');
            if(pendentes.length === 0) { tbody.innerHTML = '<tr><td colspan="5" style="text-align:center; padding:20px;">Nenhuma pendência.</td></tr>'; return; }
            tbody.innerHTML = pendentes.map(r => `<tr><td>#${r.originalIdx}</td><td>${r.data}</td><td>${r.responsavel}</td><td><span style="color:orange">${r.status}</span></td><td><button class="btn btn-success" style="padding:2px 10px;" onclick="aprovarRDO(${r.originalIdx})">Aprovar</button></td></tr>`).join('');
        }

        function aprovarRDO(idx) {
            const rdos = JSON.parse(localStorage.getItem('relatorios'));
            rdos[idx].status = 'Aprovado';
            localStorage.setItem('relatorios', JSON.stringify(rdos));
            alert("RDO Aprovado!");
            renderAprovacoes();
            atualizarDashboard();
        }

        function salvarMedicao() {
            const item = document.querySelector('#medicoes input[type="text"]').value;
            const qtd = document.querySelector('#medicoes input[type="number"]').value;
            if(!item || !qtd) return alert("Preencha item e quantidade");
            const medicoes = JSON.parse(localStorage.getItem('medicoes'));
            medicoes.push({item, qtd, data: new Date().toLocaleDateString()});
            localStorage.setItem('medicoes', JSON.stringify(medicoes));
            renderMedicoes();
            alert("Medição Salva!");
        }

        function renderMedicoes() {
            const medicoes = JSON.parse(localStorage.getItem('medicoes'));
            const tbody = document.getElementById('lista-medicoes');
            if(tbody) { tbody.innerHTML = medicoes.map(m => `<tr><td>${m.item}</td><td>${m.qtd}</td><td>${m.data}</td></tr>`).join(''); }
        }

        function salvarChecklist() {
            const tipo = document.getElementById('checklist-tipo').value;
            const checks = JSON.parse(localStorage.getItem('checklists'));
            checks.push({tipo, data: new Date().toLocaleDateString()});
            localStorage.setItem('checklists', JSON.stringify(checks));
            alert("Checklist digital salvo no servidor!");
        }

        function logout() { location.reload(); }
        
        function exportarDados() { 
            const blob = new Blob([JSON.stringify(localStorage)], {type: 'application/json'}); 
            const a = document.createElement('a'); 
            a.href = URL.createObjectURL(blob); 
            a.download = 'backup_diario_pro.json'; 
            a.click(); 
        }

        function importarDados() {
            const input = document.createElement('input');
            input.type = 'file';
            input.accept = '.json';
            input.onchange = e => {
                const file = e.target.files[0];
                const reader = new FileReader();
                reader.onload = event => {
                    try {
                        const data = JSON.parse(event.target.result);
                        localStorage.clear();
                        for (const key in data) { localStorage.setItem(key, data[key]); }
                        alert("Sistema restaurado com sucesso!");
                        location.reload();
                    } catch(err) { alert("Erro ao ler arquivo de backup."); }
                };
                reader.readAsText(file);
            };
            input.click();
        }

        function limparTudo() { if(confirm("Apagar todos os dados permanentemente?")) { localStorage.clear(); location.reload(); } }
    </script>
</body>
</html>

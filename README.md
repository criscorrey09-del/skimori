<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Skimori - Compras coletivas do Japão</title>

  <!-- Google Fonts: Fredoka & Plus Jakarta Sans -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@500;600;700&family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">

  <!-- Font Awesome -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css" />

  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            lilac: { light: '#F5EEFF', DEFAULT: '#B992EB', dark: '#8E62CC' },
            sakura: { light: '#FFF0F6', DEFAULT: '#F5B5D2', dark: '#DC77A4' },
            ink: '#2A233A',
            bgBase: '#FAF7FD'
          },
          fontFamily: {
            sans: ['"Plus Jakarta Sans"', 'sans-serif'],
            display: ['"Fredoka"', 'sans-serif']
          },
          boxShadow: {
            kawaii: '0 10px 30px -6px rgba(142, 98, 204, 0.16)',
            card: '0 8px 22px -6px rgba(42, 35, 58, 0.08)'
          }
        }
      }
    };

    function handleBannerError(imgEl) {
      if (!imgEl.dataset.fallbackApplied) {
        imgEl.dataset.fallbackApplied = 'true';
        imgEl.classList.add('hidden');
        const box = document.getElementById('banner-fallback-box');
        if (box) box.classList.remove('hidden');
      }
    }
  </script>

  <style>
    body { background-color: #FAF7FD; color: #2A233A; overflow-x: hidden; }
    .sakura-bg {
      background-image:
        radial-gradient(circle at 12% 20%, rgba(245, 181, 210, 0.35) 0%, transparent 28%),
        radial-gradient(circle at 88% 80%, rgba(185, 146, 235, 0.35) 0%, transparent 32%),
        linear-gradient(135deg, #FBF8FF 0%, #F4ECFC 100%);
    }
    .custom-scroll::-webkit-scrollbar { width: 6px; height: 6px; }
    .custom-scroll::-webkit-scrollbar-track { background: #F5EEFF; border-radius: 999px; }
    .custom-scroll::-webkit-scrollbar-thumb { background: #B992EB; border-radius: 999px; }
    @keyframes popIn {
      from { opacity: 0; transform: translateY(10px) scale(0.98); }
      to { opacity: 1; transform: translateY(0) scale(1); }
    }
    .animate-pop { animation: popIn 0.25s ease-out forwards; }
  </style>
</head>
<body class="min-h-screen flex flex-col font-sans antialiased">

  <!-- Notificação Toast -->
  <div id="toast-container" class="fixed top-4 right-4 z-50 flex flex-col gap-2 pointer-events-none max-w-sm w-full px-4 sm:px-0"></div>

  <!-- Barra de Navegação Superior (Acesso Direto - Sem Login) -->
  <header class="sticky top-0 z-40 bg-white/95 backdrop-blur-md border-b border-lilac/35 shadow-sm">
    <div class="max-w-6xl mx-auto px-4 py-3 flex flex-wrap items-center justify-between gap-3">
      <button onclick="navigateTo('catalog')" class="flex items-center gap-3 text-left group focus:outline-none">
        <div class="w-11 h-11 rounded-2xl bg-gradient-to-br from-lilac to-sakura p-0.5 shadow-sm group-hover:scale-105 transition-transform">
          <div class="w-full h-full bg-ink rounded-[14px] flex items-center justify-center relative">
            <svg class="w-7 h-7" viewBox="0 0 64 64" fill="none">
              <path d="M22 14L26 7L32 12L38 7L42 14H22Z" fill="#F5B5D2"/>
              <circle cx="32" cy="36" r="18" fill="#2A233A"/>
              <circle cx="25" cy="35" r="3.5" fill="#FFFFFF"/>
              <circle cx="26" cy="34.5" r="1.8" fill="#2A233A"/>
              <path d="M36 35C38 33 41 33 43 35" stroke="#FFFFFF" stroke-width="2.5" stroke-linecap="round"/>
            </svg>
          </div>
        </div>
        <div>
          <span class="font-display font-bold text-xl sm:text-2xl text-ink tracking-tight block leading-none">
            Skimori <span class="text-sakura-dark text-sm">🌸</span>
          </span>
          <span class="text-[11px] font-bold text-lilac-dark tracking-wide">Compras coletivas do Japão</span>
        </div>
      </button>

      <!-- Menu de Navegação -->
      <nav class="flex flex-wrap items-center gap-1.5 sm:gap-2">
        <button id="nav-catalog" onclick="navigateTo('catalog')" class="nav-btn px-3.5 py-2 rounded-2xl text-xs sm:text-sm font-extrabold transition-all bg-lilac text-white shadow-sm">
          <i class="fa-solid fa-store mr-1"></i> Catálogos
        </button>
        <button id="nav-publish" onclick="navigateTo('publish')" class="nav-btn px-3.5 py-2 rounded-2xl text-xs sm:text-sm font-extrabold transition-all bg-sakura-light text-sakura-dark border border-sakura hover:bg-sakura hover:text-ink">
          <i class="fa-solid fa-camera-retro mr-1"></i> Publicar Itens
        </button>
        <button id="nav-checkout" onclick="navigateTo('checkout')" class="nav-btn px-3.5 py-2 rounded-2xl text-xs sm:text-sm font-extrabold transition-all bg-lilac-light text-ink hover:bg-lilac/30 relative">
          <i class="fa-solid fa-pix mr-1 text-lilac-dark"></i> Pagar & Comprovante
          <span id="cart-badge" class="ml-1 inline-flex items-center justify-center px-2 py-0.5 text-[11px] font-extrabold rounded-full bg-sakura-dark text-white">0</span>
        </button>
        <button id="nav-sheets" onclick="navigateTo('sheets')" class="nav-btn px-3.5 py-2 rounded-2xl text-xs sm:text-sm font-extrabold transition-all bg-lilac-light text-ink hover:bg-lilac/30 relative">
          <i class="fa-solid fa-table-list mr-1 text-lilac-dark"></i> Planilhas Privadas
          <span id="pending-badge" class="ml-1 inline-flex items-center justify-center px-2 py-0.5 text-[11px] font-extrabold rounded-full bg-amber-500 text-white">0</span>
        </button>
      </nav>
    </div>
  </header>

  <!-- CONTEÚDO PRINCIPAL -->
  <main class="flex-1 max-w-6xl w-full mx-auto px-4 py-6 sm:py-8 space-y-8">

    <!-- ================= CATÁLOGO ================= -->
    <section id="view-catalog" class="space-y-6 animate-pop">
      <!-- Banner Oficial Skimori com o Gatinho -->
      <div class="relative overflow-hidden rounded-3xl border-2 border-lilac/45 shadow-kawaii bg-[#F5EFFB]">
        <img
          id="official-banner-img"
          src="1794540993951.jpg"
          alt="Skimori - Compras coletivas do Japão"
          class="w-full h-auto max-h-[360px] object-cover object-center block select-none"
          onerror="handleBannerError(this)"
        />
        <div id="banner-fallback-box" class="hidden sakura-bg p-6 sm:p-10 flex flex-col md:flex-row items-center justify-between gap-6">
          <div class="flex items-center gap-4">
            <div class="w-20 h-20 rounded-3xl bg-white border-2 border-sakura flex items-center justify-center shadow-sm shrink-0">
              <svg class="w-14 h-14" viewBox="0 0 64 64" fill="none">
                <circle cx="32" cy="36" r="19" fill="#2A233A"/>
              </svg>
            </div>
            <div>
              <h1 class="font-display font-bold text-3xl sm:text-4xl text-ink">Skimori</h1>
              <p class="text-sm sm:text-base font-bold text-lilac-dark">🌸 Compras coletivas do Japão 🌸</p>
            </div>
          </div>
          <button type="button" onclick="document.getElementById('banner-upload-input').click()" class="px-5 py-3 rounded-2xl bg-lilac-dark text-white text-xs sm:text-sm font-extrabold shadow-md cursor-pointer">
            <i class="fa-solid fa-image mr-1.5"></i> Carregar Foto do Banner
          </button>
        </div>
        <input id="banner-upload-input" type="file" accept="image/*" class="hidden" onchange="handleBannerUpload(event)" />
        <button type="button" onclick="document.getElementById('banner-upload-input').click()" class="absolute bottom-3 right-3 px-4 py-2 rounded-full bg-white/95 backdrop-blur-sm border border-lilac text-xs font-extrabold text-ink shadow-md hover:bg-lilac-light transition-all flex items-center gap-1.5 cursor-pointer">
          <i class="fa-solid fa-camera text-lilac-dark"></i>
          <span>Trocar Foto do Banner</span>
        </button>
      </div>

      <!-- Barra de Ações Rápidas -->
      <div class="bg-white rounded-3xl border border-lilac/40 p-4 sm:p-5 shadow-card flex flex-col md:flex-row items-center justify-between gap-4">
        <div class="flex items-center gap-3 text-center md:text-left">
          <div class="w-11 h-11 rounded-2xl bg-lilac-light text-lilac-dark flex items-center justify-center text-xl shrink-0">
            <i class="fa-solid fa-sparkles"></i>
          </div>
          <div>
            <h2 class="font-display font-bold text-lg sm:text-xl text-ink">Catálogos Skimori</h2>
            <p class="text-xs sm:text-sm text-ink/70">Adicione novos itens com fotos, nomes e valores para compra imediata.</p>
          </div>
        </div>
        <div class="flex flex-wrap items-center justify-center gap-2 shrink-0">
          <button onclick="openCreateCatalogModal()" class="px-4 py-2.5 rounded-2xl bg-lilac-light text-lilac-dark border border-lilac/50 text-xs font-extrabold hover:bg-lilac hover:text-white transition-all">
            <i class="fa-solid fa-folder-plus mr-1"></i> + Novo Catálogo
          </button>
          <button onclick="navigateTo('publish')" class="px-4 py-2.5 rounded-2xl bg-gradient-to-r from-lilac-dark to-sakura-dark text-white text-xs font-extrabold shadow-sm hover:opacity-95 transition-all">
            <i class="fa-solid fa-plus-circle mr-1"></i> + Publicar Foto & Valor
          </button>
        </div>
      </div>

      <!-- Abas de Catálogos -->
      <div id="catalog-tabs-bar" class="flex flex-wrap items-center gap-2"></div>

      <!-- Grade de Cards / Produtos -->
      <div id="products-grid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6"></div>

      <!-- Rodapé do Catálogo -->
      <div class="bg-white rounded-3xl border-2 border-lilac/45 p-5 shadow-kawaii flex flex-col sm:flex-row items-center justify-between gap-4">
        <div>
          <p class="text-xs font-extrabold uppercase tracking-wider text-lilac-dark">Carrinho & Pagamento Automático</p>
          <p id="quick-cart-summary" class="text-sm sm:text-base font-extrabold text-ink">Nenhum item selecionado</p>
        </div>
        <button onclick="navigateTo('checkout')" class="w-full sm:w-auto px-6 py-3.5 rounded-2xl bg-gradient-to-r from-lilac-dark to-sakura-dark text-white font-extrabold text-sm shadow-md hover:opacity-95 transition-all flex items-center justify-center gap-2">
          <span>Ir para Pagamento & Comprovante</span>
          <i class="fa-solid fa-arrow-right"></i>
        </button>
      </div>
    </section>

    <!-- ================= PUBLICAR ITENS ================= -->
    <section id="view-publish" class="hidden space-y-6 animate-pop">
      <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 bg-white rounded-3xl border border-lilac/40 p-5 shadow-card">
        <div>
          <span class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-sakura-light text-sakura-dark text-xs font-extrabold mb-1">
            <i class="fa-solid fa-wand-magic-sparkles"></i> Edição Livre • Sem Login
          </span>
          <h2 class="font-display font-bold text-2xl text-ink">Publicar Fotos, Nomes e Valores</h2>
          <p class="text-xs sm:text-sm text-ink/70">Cadastre novos produtos para aparecerem instantaneamente no site.</p>
        </div>
        <button onclick="navigateTo('catalog')" class="px-4 py-2.5 rounded-2xl bg-lilac-light text-lilac-dark text-xs font-extrabold hover:bg-lilac hover:text-white transition-all self-start sm:self-center">
          <i class="fa-solid fa-eye mr-1"></i> Ver Catálogos
        </button>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
        <!-- Coluna Esquerda: Catálogos e Pix -->
        <div class="lg:col-span-5 bg-white rounded-3xl border-2 border-lilac/40 p-5 sm:p-6 shadow-card space-y-5 self-start">
          <h3 class="font-display font-bold text-lg text-ink flex items-center gap-2">
            <i class="fa-solid fa-folder-tree text-lilac-dark"></i>
            <span>1. Catálogos Separados</span>
          </h3>
          <form onsubmit="handleAddCatalog(event)" class="flex items-center gap-2">
            <input type="text" id="new-catalog-input" required placeholder="Nome do catálogo..." class="flex-1 px-4 py-2.5 rounded-2xl bg-bgBase border border-lilac/50 text-xs font-semibold text-ink focus:outline-none" />
            <button type="submit" class="px-4 py-2.5 rounded-2xl bg-lilac-dark text-white font-extrabold text-xs">Criar</button>
          </form>
          <div id="manage-catalogs-list" class="space-y-2 max-h-48 overflow-y-auto custom-scroll pr-1"></div>

          <div class="pt-3 border-t border-lilac/25 space-y-3">
            <h4 class="font-display font-bold text-base text-ink flex items-center gap-2">
              <i class="fa-solid fa-pix text-sakura-dark"></i>
              <span>Link de Pagamento / Chave Pix</span>
            </h4>
            <input type="text" id="quick-pix-input" placeholder="Cole o link de pagamento ou chave Pix..." class="w-full px-3.5 py-2.5 rounded-2xl bg-bgBase border border-lilac/50 text-xs font-semibold text-ink focus:outline-none" />
            <button type="button" onclick="saveQuickPixConfig()" class="w-full py-2.5 rounded-2xl bg-ink text-white text-xs font-extrabold hover:bg-lilac-dark">Salvar Link / Chave Pix</button>
          </div>
        </div>

        <!-- Coluna Direita: Formulário do Card -->
        <div class="lg:col-span-7 bg-white rounded-3xl border-2 border-sakura p-5 sm:p-6 shadow-kawaii space-y-5">
          <h3 class="font-display font-bold text-lg text-ink flex items-center gap-2">
            <i class="fa-solid fa-image text-sakura-dark"></i>
            <span>2. Postar Novo Card</span>
          </h3>
          <form id="publish-card-form" onsubmit="handlePublishCard(event)" class="space-y-4">
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
              <div>
                <label class="block text-xs font-extrabold text-ink mb-1.5">Catálogo de Destino *</label>
                <select id="card-category-select" required class="w-full px-3.5 py-3 rounded-2xl bg-bgBase border border-lilac/50 text-xs font-bold text-ink focus:outline-none"></select>
              </div>
              <div>
                <label class="block text-xs font-extrabold text-ink mb-1.5">Valor do Item (R$) *</label>
                <input type="number" step="0.01" min="0" id="card-price-input" required placeholder="Ex: 45.00" class="w-full px-3.5 py-3 rounded-2xl bg-bgBase border border-lilac/50 text-sm font-extrabold text-lilac-dark focus:outline-none" />
              </div>
            </div>
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
              <div>
                <label class="block text-xs font-extrabold text-ink mb-1.5">Nome do Item *</label>
                <input type="text" id="card-title-input" required placeholder="Ex: Photocard Stray Kids" class="w-full px-3.5 py-3 rounded-2xl bg-bgBase border border-lilac/50 text-xs font-semibold text-ink focus:outline-none" />
              </div>
              <div>
                <label class="block text-xs font-extrabold text-ink mb-1.5">Etiqueta / Selo</label>
                <input type="text" id="card-badge-input" value="🌸 Lote Japão" class="w-full px-3.5 py-3 rounded-2xl bg-bgBase border border-lilac/50 text-xs font-semibold text-ink focus:outline-none" />
              </div>
            </div>
            <div>
              <label class="block text-xs font-extrabold text-ink mb-1.5">Descrição (Opcional)</label>
              <input type="text" id="card-desc-input" placeholder="Ex: Frete internacional incluso" class="w-full px-3.5 py-3 rounded-2xl bg-bgBase border border-lilac/50 text-xs font-semibold text-ink focus:outline-none" />
            </div>
            <div>
              <label class="block text-xs font-extrabold text-ink mb-1.5">Foto do Item *</label>
              <input id="card-photo-input" type="file" accept="image/*" class="hidden" onchange="handleCardPhotoPreview(event)" />
              <div onclick="document.getElementById('card-photo-input').click()" class="flex flex-col items-center justify-center w-full p-6 border-2 border-dashed border-lilac rounded-3xl bg-bgBase/80 hover:bg-lilac-light/50 cursor-pointer text-center">
                <div id="card-photo-empty" class="space-y-2">
                  <div class="w-12 h-12 rounded-2xl bg-white text-lilac-dark flex items-center justify-center text-xl mx-auto shadow-sm">
                    <i class="fa-solid fa-cloud-arrow-up"></i>
                  </div>
                  <p class="text-xs sm:text-sm font-extrabold text-ink">Clique para escolher a foto</p>
                </div>
                <div id="card-photo-preview-wrap" class="hidden space-y-2">
                  <img id="card-photo-preview-img" src="" alt="Prévia" class="max-h-40 mx-auto rounded-2xl border-2 border-lilac object-contain" />
                  <span class="text-xs font-extrabold text-lilac-dark underline">Trocar foto</span>
                </div>
              </div>
            </div>
            <button type="submit" class="w-full py-3.5 rounded-2xl bg-gradient-to-r from-lilac-dark to-sakura-dark text-white font-extrabold text-sm shadow-md">Publicar Card</button>
          </form>
        </div>
      </div>
    </section>

    <!-- ================= PAGAMENTO & COMPROVANTE ================= -->
    <section id="view-checkout" class="hidden space-y-6 animate-pop">
      <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 bg-white rounded-3xl border border-lilac/40 p-5 shadow-card">
        <div>
          <h2 class="font-display font-bold text-2xl text-ink">Pagamento Automático & Comprovante</h2>
          <p class="text-xs sm:text-sm text-ink/70">Pague o valor exato e envie o comprovante para confirmação.</p>
        </div>
        <button onclick="navigateTo('catalog')" class="px-4 py-2.5 rounded-2xl bg-lilac-light text-lilac-dark text-xs font-extrabold">Voltar</button>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
        <!-- Coluna Esquerda: Resumo & Pagamento -->
        <div class="lg:col-span-6 space-y-5">
          <div class="bg-white rounded-3xl border-2 border-lilac/40 p-5 sm:p-6 shadow-card space-y-4">
            <h3 class="font-display font-bold text-lg text-ink flex items-center gap-2">
              <i class="fa-solid fa-bag-shopping text-lilac-dark"></i>
              <span>Itens Selecionados</span>
            </h3>
            <div id="checkout-items-container" class="space-y-3"></div>
            <div class="bg-gradient-to-r from-lilac-light to-sakura-light rounded-2xl p-4 border border-lilac/40 flex items-center justify-between">
              <span class="text-xs font-extrabold uppercase tracking-wider text-ink/75">Total a Pagar</span>
              <span id="checkout-total-display" class="text-2xl font-extrabold text-ink">R$ 0,00</span>
            </div>
          </div>

          <div class="bg-white rounded-3xl border-2 border-sakura p-5 sm:p-6 shadow-kawaii space-y-4">
            <div class="flex items-center justify-between">
              <span class="text-xs font-extrabold text-sakura-dark">Etapa 1 • Realizar Pagamento</span>
              <button type="button" onclick="togglePixEditor()" class="text-xs font-extrabold text-lilac-dark hover:underline">Editar Link/Pix</button>
            </div>
            <p id="payment-instruction-text" class="text-xs sm:text-sm text-ink/80">Copie a chave Pix ou abra o link de pagamento abaixo.</p>

            <div id="pix-editor-box" class="hidden bg-lilac-light/70 rou
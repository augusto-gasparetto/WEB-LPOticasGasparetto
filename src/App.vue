<script setup>
import { ref, reactive, computed } from 'vue'
import emailjs from '@emailjs/browser' // npm i @emailjs/browser

/* ================= CONFIGURAÇÕES (edite aqui) ================= */
const WHATSAPP_NUMBER = '554933232399' // DDI + DDD + número, só dígitos
const MENSAGEM_BASE = 'Vim pela campanha e gostaria de mais informações.'

// Imagens (deixe '' para mostrar o espaço reservado)
const CAMPANHA_IMG = 'campanha-2026-2.jpeg'
const LOGO_IMG = 'logo.png'
const LOJA_IMG = 'loja.webp'

// EmailJS (https://dashboard.emailjs.com)
const EMAILJS_SERVICE_ID = 'service_alu443z'
const EMAILJS_TEMPLATE_ID = 'template_790jucj'
const EMAILJS_PUBLIC_KEY = '9hG3P5izs3i2c0nQx'

// Loja
const ENDERECO = 'Rua Barão do Rio Branco, 284, Sala 10, Centro, Chapecó, SC'
const TELEFONE_LOJA = '(49) 3323-2399'
const HORARIOS = [
  { dia: 'Segunda a sexta', horas: '8h30 às 12h e 13h30 às 18h' },
  { dia: 'Sábado', horas: '8h às 12h' },
]
/* ============================================================== */

// Variável com os dados do formulário
const lead = reactive({ nome: '', telefone: '' })
const tentouEnviar = ref(false)
const enviado = ref(false)

const nomeValido = computed(() => lead.nome.trim().length >= 2)
const telefoneValido = computed(() => lead.telefone.replace(/\D/g, '').length >= 10)

const whatsappUrl = computed(() => {
  const nome = lead.nome.trim()
  const texto = nome
    ? `Olá! Meu nome é ${nome}. ${MENSAGEM_BASE}`
    : `Olá! ${MENSAGEM_BASE}`
  return `https://wa.me/${WHATSAPP_NUMBER}?text=${encodeURIComponent(texto)}`
})

const mapaEmbed = `https://maps.google.com/maps?q=${encodeURIComponent(ENDERECO)}&z=16&output=embed`
const mapaRota = `https://www.google.com/maps/dir/?api=1&destination=${encodeURIComponent(ENDERECO)}`

function mascararTelefone(e) {
  let d = e.target.value.replace(/\D/g, '').slice(0, 11)
  if (d.length > 10) d = d.replace(/^(\d{2})(\d{5})(\d{4}).*/, '($1) $2-$3')
  else if (d.length > 6) d = d.replace(/^(\d{2})(\d{4})(\d{0,4}).*/, '($1) $2-$3')
  else if (d.length > 2) d = d.replace(/^(\d{2})(\d*)/, '($1) $2')
  else if (d.length > 0) d = d.replace(/^(\d*)/, '($1')
  lead.telefone = d
}

// Nome + telefone vão para o e-mail da empresa
function enviarEmail() {
  if (EMAILJS_SERVICE_ID.startsWith('SEU_')) {
    console.warn('EmailJS não configurado: preencha as constantes no topo do arquivo.')
    return Promise.resolve()
  }
  return emailjs.send(
    EMAILJS_SERVICE_ID,
    EMAILJS_TEMPLATE_ID,
    {
      nome: lead.nome.trim(),
      telefone: lead.telefone,
      whatsapp_link: `https://wa.me/55${lead.telefone.replace(/\D/g, '')}`,
      data: new Date().toLocaleString('pt-BR'),
      pagina: window.location.href,
    },
    { publicKey: EMAILJS_PUBLIC_KEY }
  )
}

function enviar() {
  tentouEnviar.value = true
  if (!nomeValido.value || !telefoneValido.value) return

  // Abre o WhatsApp na hora (evita bloqueio de pop-up) e envia o e-mail em paralelo.
  // No WhatsApp vai só o nome; o telefone fica apenas no e-mail da empresa.
  window.open(whatsappUrl.value, '_blank', 'noopener')
  enviarEmail().catch((err) => console.error('Falha ao enviar e-mail:', err))
  enviado.value = true
}

const pilares = [
  {
    titulo: 'Atendimento de perto',
    texto: 'Você é recebido pelo nome, com um cafezinho na mão, sem pressa e sem empurrar o que não precisa.',
  },
  {
    titulo: 'Armações para cada estilo',
    texto: 'Grau, sol e acessórios, com marcas como DKNY e Guess ao lado de opções para todo bolso.',
  },
  {
    titulo: 'Raiz em Chapecó',
    texto: 'Uma loja de rua, no Centro, que a cidade já conhece e à qual sempre dá para voltar.',
  },
]

const passos = [
  'Deixe seu nome e telefone.',
  'A conversa abre no WhatsApp, já com seu nome.',
  'Nossa equipe responde e combina os próximos passos.',
]
</script>

<template>
  <div class="font-sans text-[#0a173e] bg-white antialiased">
    <!-- ================= BARRA FIXA ================= -->
    <div class="sticky top-0 z-40 border-b border-white/10 bg-[#0a173e] text-white">
      <div class="mx-auto flex max-w-6xl items-center justify-between px-5 py-3">
        <a href="#" aria-label="Voltar ao topo">
          <img v-if="LOGO_IMG" :src="LOGO_IMG" alt="Óticas Gasparetto" class="h-10 w-auto" />
          <div v-else class="leading-none">
            <span class="block text-[0.7rem] tracking-[0.35em] text-[#ffca00]">ÓTICAS</span>
            <span class="block text-xl font-semibold tracking-[0.12em]">GASPARETTO</span>
          </div>
        </a>
        <nav class="flex items-center gap-4 text-sm text-white/80 sm:gap-6" aria-label="Seções">
          <a href="#sobre" class="hidden hover:text-[#ffca00] sm:inline">Quem somos</a>
          <a href="#loja" class="hidden hover:text-[#ffca00] sm:inline">Tome um café com a gente</a>
          <a href="#contato" class="rounded-full bg-[#ffca00] px-4 py-2 font-semibold text-[#0a173e] hover:brightness-95">Falar com a equipe</a>
        </nav>
      </div>
    </div>

    <!-- ================= HERO ================= -->
    <header id="contato" class="relative scroll-mt-20 overflow-hidden bg-[#0a173e] text-white">
      <div class="pointer-events-none absolute -right-44 -top-44 h-[38rem] w-[38rem] rounded-full border-[3.5rem] border-[#ffca00]/10" aria-hidden="true"></div>
      <div class="pointer-events-none absolute -bottom-32 -left-32 h-80 w-80 rounded-full bg-[#ffca00]/[0.06]" aria-hidden="true"></div>

      <div class="relative mx-auto max-w-6xl px-5 pb-16 pt-10 md:pb-24 md:pt-14">
        <div class="grid items-center gap-10 md:grid-cols-2 md:gap-14">
          <div class="order-2 md:order-1">
            <h1 class="text-4xl font-semibold leading-[1.08] sm:text-5xl">
              Seu olhar merece atenção de verdade.
            </h1>
            <p class="mt-5 max-w-md text-lg leading-relaxed text-white/75">
              Veja a campanha do momento e fale com a gente no WhatsApp. Do outro lado tem gente de Chapecó, pronta para ajudar você a escolher bem. E, se preferir, venha tomar um cafézinho com a gente na loja.
            </p>

            <form
              class="mt-8 rounded-2xl bg-white p-5 text-[#0a173e] shadow-2xl shadow-black/30 sm:p-6"
              novalidate
              @submit.prevent="enviar"
            >
              <p class="text-lg font-semibold">Quero falar com a equipe</p>
              <p class="mt-1 text-sm text-[#0a173e]/65">Informe seus dados e a conversa abre no WhatsApp.</p>

              <div class="mt-4">
                <label for="nome" class="mb-1 block text-sm font-medium">Seu nome</label>
                <input
                  id="nome"
                  v-model="lead.nome"
                  type="text"
                  autocomplete="name"
                  placeholder="Como podemos te chamar?"
                  class="w-full rounded-lg border px-4 py-3 outline-none transition focus:ring-2 focus:ring-[#ffca00]"
                  :class="tentouEnviar && !nomeValido ? 'border-red-500' : 'border-[#0a173e]/25'"
                />
                <p v-if="tentouEnviar && !nomeValido" class="mt-1 text-sm text-red-600">Digite seu nome para continuar.</p>
              </div>

              <div class="mt-4">
                <label for="telefone" class="mb-1 block text-sm font-medium">Seu telefone</label>
                <input
                  id="telefone"
                  :value="lead.telefone"
                  type="tel"
                  inputmode="numeric"
                  autocomplete="tel"
                  placeholder="(49) 99999-9999"
                  class="w-full rounded-lg border px-4 py-3 outline-none transition focus:ring-2 focus:ring-[#ffca00]"
                  :class="tentouEnviar && !telefoneValido ? 'border-red-500' : 'border-[#0a173e]/25'"
                  @input="mascararTelefone"
                />
                <p v-if="tentouEnviar && !telefoneValido" class="mt-1 text-sm text-red-600">Informe o telefone com DDD.</p>
              </div>

              <button
                type="submit"
                class="mt-5 flex w-full items-center justify-center gap-2 rounded-lg bg-[#ffca00] px-5 py-4 font-semibold text-[#0a173e] transition hover:brightness-95 active:scale-[0.99] focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-[#0a173e]"
              >
                <svg viewBox="0 0 24 24" class="h-5 w-5" fill="currentColor" aria-hidden="true">
                  <path d="M12.04 2a9.9 9.9 0 0 0-8.43 15.1L2 22l5-1.58A9.9 9.9 0 1 0 12.04 2Zm5.8 14.04c-.25.7-1.45 1.34-2 1.4-.51.06-1.15.09-1.86-.12a17 17 0 0 1-1.69-.62c-2.97-1.28-4.9-4.27-5.05-4.47-.15-.2-1.2-1.6-1.2-3.05s.76-2.16 1.03-2.46c.27-.3.59-.37.79-.37h.57c.18 0 .43-.07.67.51.25.6.84 2.06.92 2.21.07.15.12.32.02.52-.1.2-.15.32-.3.5-.15.17-.31.38-.44.51-.15.15-.3.31-.13.6.17.3.77 1.27 1.65 2.05 1.13 1 2.09 1.31 2.39 1.46.3.15.47.12.65-.07.17-.2.75-.87.95-1.17.2-.3.4-.25.67-.15.27.1 1.73.82 2.03.97.3.15.5.22.57.35.07.12.07.72-.18 1.42Z" />
                </svg>
                Chamar no WhatsApp
              </button>

              <p v-if="enviado" class="mt-3 rounded-lg bg-[#ffca00]/20 px-3 py-2 text-center text-sm" role="status">
                Pronto! Abrimos o WhatsApp em outra aba.
                <a :href="whatsappUrl" target="_blank" rel="noopener" class="font-semibold underline">Não abriu? Toque aqui.</a>
              </p>
              <p v-else class="mt-3 text-center text-xs text-[#0a173e]/60">
                Ao enviar, você autoriza a Gasparetto a usar seu telefone para retornar o contato.
              </p>
            </form>
          </div>

          <!-- Campanha 1:1 -->
          <div class="order-1 md:order-2">
            <div
              class="relative mx-auto w-full max-w-md overflow-hidden rounded-2xl md:max-w-none"
              :class="CAMPANHA_IMG ? 'shadow-2xl shadow-black/30' : 'aspect-square bg-white/5 ring-1 ring-white/15'"
            >
              <!-- Proporção livre: a imagem aparece inteira, sem cortes -->
              <img v-if="CAMPANHA_IMG" :src="CAMPANHA_IMG" alt="Campanha vigente das Óticas Gasparetto" class="block h-auto w-full" />
              <div v-else class="flex h-full w-full flex-col items-center justify-center gap-2 border-2 border-dashed border-[#ffca00]/50 p-6 text-center">
                <svg viewBox="0 0 24 24" class="h-10 w-10 text-[#ffca00]" fill="none" stroke="currentColor" stroke-width="1.5" aria-hidden="true">
                  <rect x="3" y="3" width="18" height="18" rx="2" /><circle cx="9" cy="9" r="1.8" /><path d="m21 16-5-5-8 8" />
                </svg>
                <p class="font-medium">Espaço da campanha</p>
                <p class="text-sm text-white/60">Qualquer proporção, a imagem aparece inteira.<br />Defina <code class="text-[#ffca00]">CAMPANHA_IMG</code> no topo do arquivo.</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </header>

    <!-- ================= QUEM SOMOS ================= -->
    <section id="sobre" class="mx-auto max-w-6xl scroll-mt-20 px-5 py-16 md:py-24">
      <div class="grid items-center gap-10 md:grid-cols-[0.9fr_1.1fr] md:gap-16">
        <div class="relative">
          <div class="absolute -bottom-4 -left-4 h-full w-full rounded-2xl bg-[#ffca00]" aria-hidden="true"></div>
          <div class="relative aspect-[4/5] overflow-hidden rounded-2xl bg-[#0a173e]/[0.06]">
            <img v-if="LOJA_IMG" :src="LOJA_IMG" alt="Fachada da Óticas Gasparetto" class="h-full w-full object-cover" />
            <div v-else class="flex h-full flex-col items-center justify-center gap-1 p-6 text-center text-[#0a173e]/60">
              <p class="font-medium text-[#0a173e]">Foto da loja ou da equipe</p>
              <p class="text-sm">Defina <code>LOJA_IMG</code> no topo do arquivo.</p>
            </div>
          </div>
        </div>

        <div>
          <h2 class="text-3xl font-semibold leading-tight sm:text-4xl">
            Uma ótica de Chapecó, aberta desde 1989
          </h2>
          <div class="mt-5 space-y-4 text-lg leading-relaxed text-[#0a173e]/80">
            <p>
              A Gasparetto começou em 1989, no Centro de Chapecó, e atravessou mais de três décadas
              cuidando da visão de gerações de famílias da região.
            </p>
            <p>
              Aqui, óculos não são só um produto. Escolher uma armação é escolher como você quer
              ser visto, e a gente faz questão de acompanhar essa escolha de perto.
            </p>
          </div>

          <ul class="mt-8 space-y-5">
            <li v-for="p in pilares" :key="p.titulo" class="flex gap-4">
              <span class="mt-1 flex h-6 w-6 shrink-0 items-center justify-center rounded-full bg-[#ffca00]" aria-hidden="true">
                <svg viewBox="0 0 24 24" class="h-3.5 w-3.5" fill="none" stroke="#0a173e" stroke-width="3"><path d="m5 12 5 5 9-10" /></svg>
              </span>
              <div>
                <h3 class="font-semibold">{{ p.titulo }}</h3>
                <p class="text-[#0a173e]/70">{{ p.texto }}</p>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </section>

    <!-- ================= COMO FUNCIONA ================= -->
    <section class="bg-[#0a173e]/[0.04]">
      <div class="mx-auto max-w-6xl px-5 py-14 md:py-16">
        <h2 class="text-2xl font-semibold sm:text-3xl">Como funciona</h2>
        <ol class="mt-8 grid gap-6 md:grid-cols-3">
          <li v-for="(p, i) in passos" :key="p" class="flex items-start gap-4">
            <span class="flex h-10 w-10 shrink-0 items-center justify-center rounded-full bg-[#ffca00] text-lg font-semibold">{{ i + 1 }}</span>
            <p class="pt-1.5 text-lg">{{ p }}</p>
          </li>
        </ol>
      </div>
    </section>

    <!-- ================= LOCALIZAÇÃO ================= -->
    <section id="loja" class="mx-auto max-w-6xl scroll-mt-20 px-5 py-16 md:py-24">
      <h2 class="max-w-xl text-3xl font-semibold leading-tight sm:text-4xl">
        Venha tomar um cafézinho com a gente
      </h2>
      <p class="mt-3 max-w-xl text-lg text-[#0a173e]/75">
        A porta está aberta, no coração de Chapecó. Passe aqui, experimente as armações com calma,
        tire suas dúvidas e saia com o óculos certo. O café é por nossa conta.
      </p>

      <div class="mt-10 grid gap-6 md:grid-cols-[0.8fr_1.2fr]">
        <div class="flex flex-col justify-between rounded-2xl bg-[#0a173e] p-6 text-white sm:p-8">
          <div class="space-y-5">
            <div>
              <p class="text-sm text-[#ffca00]">Endereço</p>
              <p class="mt-1 text-lg">{{ ENDERECO }}</p>
            </div>
            <div>
              <p class="text-sm text-[#ffca00]">Horário</p>
              <ul class="mt-1 space-y-1 text-lg">
                <li v-for="h in HORARIOS" :key="h.dia">
                  <span class="font-semibold">{{ h.dia }}:</span> {{ h.horas }}
                </li>
              </ul>
            </div>
            <div>
              <p class="text-sm text-[#ffca00]">Telefone</p>
              <p class="mt-1 text-lg">{{ TELEFONE_LOJA }}</p>
            </div>
          </div>
          <div class="mt-8 flex flex-col gap-3 sm:flex-row md:flex-col lg:flex-row">
            <a :href="mapaRota" target="_blank" rel="noopener" class="inline-flex flex-1 items-center justify-center rounded-lg bg-[#ffca00] px-5 py-3 font-semibold text-[#0a173e] hover:brightness-95 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-white">
              Como chegar
            </a>
            <a :href="whatsappUrl" target="_blank" rel="noopener" class="inline-flex flex-1 items-center justify-center rounded-lg border border-white/30 px-5 py-3 font-semibold hover:border-[#ffca00] hover:text-[#ffca00] focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-white">
              Falar no WhatsApp
            </a>
          </div>
        </div>

        <div class="min-h-[22rem] overflow-hidden rounded-2xl ring-1 ring-[#0a173e]/15">
          <iframe
            :src="mapaEmbed"
            title="Mapa com a localização das Óticas Gasparetto"
            class="h-full min-h-[22rem] w-full border-0"
            loading="lazy"
            referrerpolicy="no-referrer-when-downgrade"
          ></iframe>
        </div>
      </div>
    </section>

    <!-- ================= CTA FINAL ================= -->
    <section class="bg-[#ffca00]">
      <div class="mx-auto flex max-w-6xl flex-col items-start gap-5 px-5 py-12 md:flex-row md:items-center md:justify-between">
        <h2 class="max-w-xl text-2xl font-semibold leading-snug sm:text-3xl">
          Ficou com alguma dúvida? Mande um oi ou passe aqui para um cafezinho.
        </h2>
        <a href="#contato" class="inline-flex shrink-0 items-center justify-center rounded-lg bg-[#0a173e] px-6 py-4 font-semibold text-white hover:bg-[#13245a] focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-[#0a173e]">
          Quero falar com a equipe
        </a>
      </div>
    </section>

    <footer class="bg-[#0a173e] text-sm text-white/75">
      <div class="mx-auto flex max-w-6xl flex-col gap-2 px-5 py-8 md:flex-row md:items-center md:justify-between">
        <p class="font-semibold text-white">Óticas Gasparetto</p>
        <p>{{ ENDERECO }}</p>
      </div>
    </footer>

    <!-- Botão flutuante (mobile) -->
    <a
      href="#contato"
      aria-label="Ir para o formulário de contato"
      class="fixed bottom-5 right-5 z-50 flex h-14 w-14 items-center justify-center rounded-full bg-[#ffca00] text-[#0a173e] shadow-lg shadow-black/30 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-white sm:hidden"
    >
      <svg viewBox="0 0 24 24" class="h-7 w-7" fill="currentColor" aria-hidden="true">
        <path d="M12.04 2a9.9 9.9 0 0 0-8.43 15.1L2 22l5-1.58A9.9 9.9 0 1 0 12.04 2Zm5.8 14.04c-.25.7-1.45 1.34-2 1.4-.51.06-1.15.09-1.86-.12a17 17 0 0 1-1.69-.62c-2.97-1.28-4.9-4.27-5.05-4.47-.15-.2-1.2-1.6-1.2-3.05s.76-2.16 1.03-2.46c.27-.3.59-.37.79-.37h.57c.18 0 .43-.07.67.51.25.6.84 2.06.92 2.21.07.15.12.32.02.52-.1.2-.15.32-.3.5-.15.17-.31.38-.44.51-.15.15-.3.31-.13.6.17.3.77 1.27 1.65 2.05 1.13 1 2.09 1.31 2.39 1.46.3.15.47.12.65-.07.17-.2.75-.87.95-1.17.2-.3.4-.25.67-.15.27.1 1.73.82 2.03.97.3.15.5.22.57.35.07.12.07.72-.18 1.42Z" />
      </svg>
    </a>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Archivo:wght@400;500;600&display=swap');

html { scroll-behavior: smooth; }
.font-sans { font-family: 'Archivo', 'Helvetica Neue', Arial, sans-serif; }
@media (prefers-reduced-motion: reduce) {
  html { scroll-behavior: auto; }
  * { transition: none !important; }
}
</style>
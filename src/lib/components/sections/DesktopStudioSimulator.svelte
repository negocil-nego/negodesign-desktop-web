<script lang="ts">
  /**
   * Tela inicial do NegoDesign Desktop.
   * Usa o Tailwind do projeto (cores em valores arbitrários, sem config extra).
   *
   * Callbacks:
   *  - onSelect        → { id } para menu, cartões de início rápido, abrir pasta e links
   *  - onToggleStartup → boolean (checkbox "Mostrar tela ao iniciar")
   */
  interface Props {
    version?: string;
    status?: string;
    showTitleBar?: boolean;
    showOnStart?: boolean;
    onSelect?: (detail: { id: string }) => void;
    onToggleStartup?: (checked: boolean) => void;
  }

  let {
    version = "0.1.0",
    status = "Pronto",
    showTitleBar = true,
    showOnStart = $bindable(true),
    onSelect,
    onToggleStartup,
  }: Props = $props();

  const select = (id: string) => onSelect?.({ id });

  // Ícones: lista de paths (viewBox 16x16 ou 18x18 conforme o uso)
  const menu = [
    {
      id: "new",
      label: "Novo Projecto",
      paths: ["M1.5 3.5h4l1.5 1.5h7v8h-12.5z", "M11 7v4M9 9h4"],
    },
    {
      id: "templates",
      label: "Templates",
      paths: [
        "M3 2h10a1 1 0 0 1 1 1v10a1 1 0 0 1-1 1H3a1 1 0 0 1-1-1V3a1 1 0 0 1 1-1z",
        "M2 7h5M7 7v7",
      ],
    },
    {
      id: "gallery",
      label: "Galeria",
      paths: [
        "M3.5 2.5h9a2 2 0 0 1 2 2v7a2 2 0 0 1-2 2h-9a2 2 0 0 1-2-2v-7a2 2 0 0 1 2-2z",
        "M1.5 12l4-3 3 2 3-2 3 3",
      ],
    },
  ];

  const quickStart = [
    {
      id: "new-frontend",
      title: "Novo Projecto Frontend",
      description: "Svelte, React, Vue ou Flutter com Tailwind CSS nativo",
      tag: "Frontend",
      paths: ["M2 4h4.5L8 5.5h8V14H2z", "M12 8v4M10 10h4"],
    },
    {
      id: "openapi",
      title: "Gerar via OpenAPI",
      description: "Lê Swagger/Scalar e gera interface administrativa completa",
      tag: "OpenAPI",
      paths: [
        "M5 2h7a2 2 0 0 1 2 2v8a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2z",
        "M6 6h5M6 9h3",
      ],
    },
    {
      id: "templates",
      title: "Templates Prontos",
      description: "Dashboards, autenticação e tabelas de dados configuradas",
      tag: "Templates",
      paths: [
        "M4 2h10a2 2 0 0 1 2 2v10a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2z",
        "M2 8h6M8 8v8",
      ],
    },
  ];

  interface TechItem {
    name: string;
    label: string;
    color: string;
    logo?: string;
  }

  // Logótipos oficiais em /static/langs (fallback para `label` quando não houver ficheiro)
  const frontendTech: TechItem[] = [
    {
      name: "Svelte",
      label: "S",
      color: "text-orange-600",
      logo: "/langs/svelte.svg",
    },
    {
      name: "React",
      label: "R",
      color: "text-sky-500",
      logo: "/langs/react.svg",
    },
    {
      name: "Vue",
      label: "V",
      color: "text-emerald-600",
      logo: "/langs/vue-js.svg",
    },
    {
      name: "Flutter",
      label: "F",
      color: "text-blue-500",
      logo: "/langs/flutter.svg",
    },
    { name: "Tauri", label: "T", color: "text-cyan-500" },
  ];
  const backendTech: TechItem[] = [
    {
      name: "Spring Boot",
      label: "S",
      color: "text-lime-600",
      logo: "/langs/spring-boot.svg",
    },
    {
      name: "NestJS",
      label: "N",
      color: "text-rose-600",
      logo: "/langs/nestjs.svg",
    },
    {
      name: ".NET",
      label: ".NET",
      color: "text-violet-700",
      logo: "/langs/aspnet-core.svg",
    },
  ];

  const links = [
    { id: "docs", label: "Documentação" },
    { id: "help", label: "Ajuda" },
    { id: "github", label: "GitHub" },
  ];

  function onStartupChange(e: Event & { currentTarget: HTMLInputElement }) {
    const checked = e.currentTarget.checked;
    showOnStart = checked;
    onToggleStartup?.(checked);
  }
</script>

<div
  class="flex flex-1 h-full w-full md:w-11/12 lg:w-[calc(75%+70px)] xl:w-[calc(66.6667%+80px)] max-w-310 mx-auto flex-col overflow-hidden rounded-t-xl border border-b-0 border-white/15 bg-[#131316] shadow-[0_-8px_70px_rgba(76,106,245,0.22),0_28px_80px_rgba(0,0,0,0.85)] text-zinc-200 antialiased"
>
  {#if showTitleBar}
    <!-- Barra de título -->
    <header
      class="flex h-5.5 shrink-0 items-center justify-between bg-[#18181b] pl-1 text-xs text-zinc-200"
    >
      <div class="flex items-center gap-1.5">
        <span
          class="flex size-3.5 items-center justify-center rounded-xs bg-[#1f6bff] text-[8px] font-bold text-white"
          >N</span
        >
        <span>negodesign-desktop</span>
      </div>
      <div class="flex h-full items-center">
        <button
          class="flex h-full w-11.5 items-center justify-center hover:bg-white/10"
          aria-label="Minimizar"
        >
          <span class="block h-px w-2.5 bg-zinc-200"></span>
        </button>
        <button
          class="flex h-full w-11.5 items-center justify-center hover:bg-white/10"
          aria-label="Maximizar"
        >
          <span class="block size-2 border border-zinc-200"></span>
        </button>
        <button
          class="flex h-full w-11.5 items-center justify-center hover:bg-red-600"
          aria-label="Fechar"
        >
          <svg
            width="10"
            height="10"
            viewBox="0 0 10 10"
            stroke="currentColor"
            stroke-width="1"><path d="M0 0L10 10M10 0L0 10" /></svg
          >
        </button>
      </div>
    </header>
  {/if}

  <div class="flex min-h-0 flex-1">
    <!-- Barra lateral -->
    <aside
      class="hidden md:flex md:w-52 lg:w-60 xl:w-64 shrink-0 flex-col border-r border-[#26262b] bg-[#17171a]"
    >
      <div class="px-4 pt-5">
        <div class="flex items-center justify-between pb-4">
          <div class="flex items-center gap-2.5">
            <div
              class="flex size-7 items-center justify-center rounded-md bg-[#1f6bff] text-xs font-bold text-white"
            >
              N
            </div>
            <div class="leading-tight">
              <p class="text-[15px] font-bold text-zinc-50">NegoDesign</p>
              <p class="text-xs text-zinc-500">Desktop Studio</p>
            </div>
          </div>
          <button
            type="button"
            class="flex size-8 items-center justify-center rounded-sm border border-[#2f2f35] bg-[#1d1d22] text-zinc-200 hover:bg-white/5"
            aria-label="Alternar tema"
            onclick={() => select("toggle-theme")}
          >
            <svg
              width="16"
              height="16"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
            >
              <circle cx="12" cy="12" r="4" fill="currentColor" />
              <path
                d="M12 2v2M12 20v2M2 12h2M20 12h2M4.9 4.9l1.4 1.4M17.7 17.7l1.4 1.4M19.1 4.9l-1.4 1.4M6.3 17.7l-1.4 1.4"
              />
            </svg>
          </button>
        </div>
        <div class="h-px w-full bg-[#2b2b31]"></div>
      </div>

      <nav class="mt-4 px-4">
        <p
          class="mb-3 flex items-center gap-2 pl-2 text-xs font-bold tracking-wider text-zinc-300"
        >
          <svg
            width="12"
            height="12"
            viewBox="0 0 12 12"
            fill="none"
            stroke="#8a8a93"><path d="M2 8l4-4 4 4" /></svg
          >
          MENU
        </p>
        <ul class="space-y-1">
          {#each menu as item}
            <li>
              <button
                type="button"
                class="flex w-full items-center gap-3.5 rounded-sm px-3 py-2.5 text-left text-sm text-zinc-200 hover:bg-white/5"
                onclick={() => select(item.id)}
              >
                <svg
                  width="16"
                  height="16"
                  viewBox="0 0 16 16"
                  fill="none"
                  stroke="#a1a1aa"
                  stroke-width="1.3"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  {#each item.paths as d}<path {d} />{/each}
                </svg>
                {item.label}
              </button>
            </li>
          {/each}
        </ul>
      </nav>

      <div class="mt-auto px-4 pb-3">
        <div class="mb-3 h-px bg-[#2b2b31]"></div>
        <div
          class="flex items-center justify-between text-[11px] text-zinc-400"
        >
          <span class="flex items-center gap-2"
            ><span class="h-1.5 w-1.5 rounded-full bg-green-500"
            ></span>{status}</span
          >
          <span class="font-mono text-[10px] text-zinc-500">v{version}</span>
        </div>
      </div>
    </aside>

    <!-- Área principal -->
    <main
      class="relative flex min-w-0 flex-1 items-stretch justify-center overflow-auto p-3 sm:p-5 lg:p-8"
    >
      <!-- Padrão decorativo -->
      <svg
        class="pointer-events-none absolute inset-0 h-full w-full"
        preserveAspectRatio="none"
        viewBox="0 0 1664 1012"
        fill="none"
        stroke="#1d1d22"
        stroke-width="9"
        stroke-linecap="round"
        stroke-linejoin="round"
        aria-hidden="true"
      >
        <path vector-effect="non-scaling-stroke" d="M44 0V48Q44 90 86 90H124" />
        <path vector-effect="non-scaling-stroke" d="M124 0V68" />
        <path
          vector-effect="non-scaling-stroke"
          d="M264 0V38Q264 68 294 68H334"
        />
        <path vector-effect="non-scaling-stroke" d="M606 0V56" />
        <path vector-effect="non-scaling-stroke" d="M534 74H754" />
        <path vector-effect="non-scaling-stroke" d="M934 0V74" />
        <path vector-effect="non-scaling-stroke" d="M1114 74H1364" />
        <path
          vector-effect="non-scaling-stroke"
          d="M1394 0H1524Q1574 0 1574 48V74"
        />
        <path
          vector-effect="non-scaling-stroke"
          d="M44 228Q44 183 89 183H126V308Q126 350 84 350H44"
        />
        <path vector-effect="non-scaling-stroke" d="M0 523H193" />
        <path vector-effect="non-scaling-stroke" d="M0 748H193" />
        <path
          vector-effect="non-scaling-stroke"
          d="M44 818Q84 818 84 858V918"
        />
        <path vector-effect="non-scaling-stroke" d="M1472 373H1574" />
        <path
          vector-effect="non-scaling-stroke"
          d="M1504 248Q1504 213 1544 213H1594Q1639 213 1639 258V288"
        />
        <path vector-effect="non-scaling-stroke" d="M1472 523H1664" />
        <path vector-effect="non-scaling-stroke" d="M1472 748H1664" />
        <path
          vector-effect="non-scaling-stroke"
          d="M1324 1012V963Q1324 933 1359 933H1444"
        />
      </svg>

      <!-- Cartão -->
      <section
        class="relative w-full max-w-319.5 flex flex-col overflow-hidden rounded-sm border border-[#2b2b31] bg-[#1a1a1f] shadow-2xl shadow-black/40"
      >
        <!-- Hero -->
        <div
          class="relative h-52 md:h-56 lg:h-68 overflow-hidden bg-linear-to-b from-[#17181c] via-[#1a1a1f] to-[#22181c]"
        >
          <div
            class="absolute inset-x-0 bottom-0 h-3/4 bg-radial-[ellipse_at_50%_100%] from-[rgba(107,42,42,.55)] to-transparent to-65%"
          ></div>

          <!-- Se tiver a imagem real do hero, use-a aqui e remova as ilustrações abaixo:
               <img src="/hero.jpg" alt="" class="absolute inset-0 h-full w-full object-cover opacity-60" /> -->

          <div
            class="absolute left-[560px] top-0 hidden h-24 w-[215px] bg-[#14161b] md:block"
          >
            <div class="space-y-1.5 p-2 pl-12 opacity-60">
              <div class="h-[3px] w-6 bg-slate-400"></div>
              <div class="ml-3 h-[3px] w-16 bg-slate-400"></div>
              <div class="ml-3 h-[3px] w-20 bg-slate-400"></div>
              <div class="h-[3px] w-8 bg-slate-400"></div>
              <div class="h-[3px] w-12 bg-slate-400"></div>
            </div>
          </div>
          <div
            class="absolute right-0 top-0 hidden h-24 w-[406px] bg-[#13151a] lg:block"
          >
            <div class="space-y-1.5 p-2 pl-14 opacity-50">
              <div class="h-[3px] w-16 bg-slate-400"></div>
              <div class="h-[3px] w-24 bg-slate-400"></div>
              <div class="ml-3 h-[3px] w-40 bg-slate-400"></div>
              <div class="ml-3 h-[3px] w-48 bg-slate-400"></div>
              <div class="ml-3 h-[3px] w-36 bg-slate-400"></div>
              <div class="h-[3px] w-20 bg-slate-400"></div>
            </div>
          </div>
          <div
            class="absolute left-[520px] top-[60px] hidden h-57.5 w-28.75 -rotate-14 rounded-[14px] border-2 border-[#3a3a42] bg-[#202024] md:block"
          >
            <div class="m-2 h-51.25 rounded-lg bg-[#17171b] text-center">
              <p
                class="pt-14 text-xl font-extrabold tracking-wider text-[#b3342f]"
              >
                NEG
              </p>
              <div class="mx-3 mt-14 h-0.75 rounded-sm bg-[#3a3a42]"></div>
              <div class="ml-3 mt-2 h-0.75 w-14 rounded-sm bg-[#2e2e35]"></div>
            </div>
          </div>

          <div
            class="absolute inset-0 bg-linear-to-r from-[#1a1a1f]/95 via-[#1a1a1f]/0 to-transparent"
          ></div>
          <div
            class="absolute inset-0 bg-linear-to-b from-transparent via-transparent to-[#1a1a1f]/95"
          ></div>

          <div
            class="relative flex h-full flex-col justify-between p-5 md:p-6 lg:p-8"
          >
            <div class="flex items-center gap-2.5">
              <span
                class="inline-flex items-center gap-2 rounded-xs bg-[#1f6bff] px-2.5 py-1.25 text-[11px] font-bold tracking-wide text-white"
              >
                <span class="size-1.5 rounded-full bg-cyan-300"
                ></span>V{version} DESKTOP
              </span>
              <span class="text-xs font-medium text-zinc-200"
                >NegoDesign Studio</span
              >
            </div>
            <div>
              <h1 class="flex items-start gap-3 leading-none">
                <span
                  class="text-2xl sm:text-3xl lg:text-[38px] font-extrabold tracking-tighter text-white"
                  >NegoDesign</span
                >
                <span
                  class="mt-0.5 text-lg sm:text-xl lg:text-[25px] font-light text-zinc-300"
                  >Desktop</span
                >
              </h1>
              <p class="mt-2 text-xs sm:text-sm text-zinc-200">
                Ecossistema de UI multiplataforma e conversão OpenAPI para
                Frontend
              </p>
            </div>
          </div>
        </div>

        <div class="h-px bg-[#2b2b31]"></div>

        <!-- Conteúdo -->
        <div
          class="grid flex-1 gap-5 lg:gap-8 px-5 md:px-6 lg:px-8 pb-6 pt-5 lg:pt-7 md:grid-cols-2"
        >
          <!-- Início rápido -->
          <div class="flex flex-col">
            <div
              class="mb-3 flex items-end justify-between border-b border-[#2b2b31] pb-2"
            >
              <h2 class="text-xs font-semibold tracking-widest text-zinc-500">
                INÍCIO RÁPIDO
              </h2>
              <span
                class="text-[10px] font-semibold tracking-wider text-zinc-500"
                >CRIAR OU IMPORTAR</span
              >
            </div>

            <div class="space-y-2.5">
              {#each quickStart as item}
                <button
                  type="button"
                  class="flex w-full items-center gap-3.5 rounded-xs border border-[#2b2b31] bg-[#1d1d22] p-3.5 text-left hover:border-zinc-600"
                  onclick={() => select(item.id)}
                >
                  <span
                    class="flex size-9 shrink-0 items-center justify-center rounded-sm border border-[#33333a] bg-[#232328]"
                  >
                    <svg
                      width="18"
                      height="18"
                      viewBox="0 0 18 18"
                      fill="none"
                      stroke="#e4e4e7"
                      stroke-width="1.4"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                    >
                      {#each item.paths as d}<path {d} />{/each}
                    </svg>
                  </span>
                  <span class="min-w-0 flex-1">
                    <span class="block text-sm font-bold text-zinc-50"
                      >{item.title}</span
                    >
                    <span class="block text-xs text-zinc-400"
                      >{item.description}</span
                    >
                  </span>
                  <span
                    class="rounded-xs bg-[#2a2a30] px-2 py-0.5 font-mono text-[11px] text-zinc-400"
                    >{item.tag}</span
                  >
                </button>
              {/each}
            </div>

            <button
              type="button"
              class="mt-auto flex items-center gap-2 pt-6 lg:pt-10 text-left text-sm text-zinc-200 hover:text-white"
              onclick={() => select("open-folder")}
            >
              <svg width="16" height="16" viewBox="0 0 16 16"
                ><path d="M1 3h5l1.5 1.5H15V13H1z" fill="#f2b632" /><path
                  d="M1 6.5h14V13H1z"
                  fill="#e5a21c"
                /></svg
              >
              Abrir pasta de projecto existente...
            </button>
          </div>

          <!-- Ecossistema -->
          <div class="flex flex-col">
            <div
              class="mb-3 flex items-end justify-between border-b border-[#2b2b31] pb-2"
            >
              <h2 class="text-xs font-semibold tracking-widest text-zinc-500">
                ECOSSISTEMA SUPORTADO
              </h2>
              <span
                class="text-[10px] font-semibold tracking-wider text-zinc-500"
                >MULTIPLATAFORMA</span
              >
            </div>

            <div class="space-y-2.5">
              <div
                class="rounded-xs border border-[#2b2b31] bg-[#1d1d22] p-3 pb-3.5"
              >
                <div class="mb-2.5 flex items-center justify-between">
                  <span class="text-[11px] font-semibold text-zinc-50"
                    >Frontend &amp; Multiplataforma</span
                  >
                  <span class="font-mono text-[9px] text-zinc-500"
                    >Web • App • Mobile</span
                  >
                </div>
                <div class="flex flex-wrap gap-3">
                  {#each frontendTech as tech (tech.name)}
                    <span
                      title={tech.name}
                      class="flex size-10.5 items-center justify-center rounded-full bg-zinc-100 text-[15px] font-bold {tech.color}"
                    >
                      {#if tech.logo}
                        <img
                          src={tech.logo}
                          alt={tech.name}
                          class="size-6 object-contain"
                        />
                      {:else}
                        {tech.label}
                      {/if}
                    </span>
                  {/each}
                </div>
              </div>

              <div
                class="rounded-xs border border-[#2b2b31] bg-[#1d1d22] p-3 pb-3.5"
              >
                <div class="mb-2.5 flex items-center justify-between">
                  <span class="text-[11px] font-semibold text-zinc-50"
                    >Backend (OpenAPI • Swagger • Scalar)</span
                  >
                  <span class="font-mono text-[9px] text-zinc-500"
                    >DTOs &amp; Endpoints</span
                  >
                </div>
                <div class="flex flex-wrap gap-3">
                  {#each backendTech as tech (tech.name)}
                    <span
                      title={tech.name}
                      class="flex size-10.5 items-center justify-center rounded-full bg-zinc-100 font-bold {tech.color} {tech
                        .label.length > 2
                        ? 'text-xs'
                        : 'text-[15px]'}"
                    >
                      {#if tech.logo}
                        <img
                          src={tech.logo}
                          alt={tech.name}
                          class="size-6 object-contain"
                        />
                      {:else}
                        {tech.label}
                      {/if}
                    </span>
                  {/each}
                </div>
              </div>
            </div>

            <div
              class="mt-4 md:mt-auto rounded-xs border border-[#2b2b31] bg-[#1d1d22] p-3 pt-3.5"
            >
              <p class="flex items-center gap-2 text-xs font-bold text-zinc-50">
                <span class="size-1.75 rounded-full bg-[#1f6bff]"></span>
                Modo Studio Desktop Nativo
              </p>
              <p class="mt-2 text-[11px] leading-4.5 text-zinc-400">
                Gera componentes 100% nativos em Tailwind CSS. Mantém a
                consistência de UI/UX em Svelte, React, Vue, Flutter e Tauri.
              </p>
            </div>
          </div>
        </div>

        <!-- Rodapé -->
        <footer
          class="flex flex-wrap items-center justify-between gap-3 border-t border-[#2b2b31] bg-[#18181c] px-5 py-2.5 text-xs text-zinc-500"
        >
          <label class="flex cursor-pointer items-center gap-2">
            <input
              type="checkbox"
              checked={showOnStart}
              onchange={onStartupChange}
              class="size-4 rounded-xs accent-[#1f6bff]"
            />
            Mostrar tela ao iniciar o NegoDesign
          </label>
          <nav class="flex items-center gap-2.5">
            {#each links as link, i}
              <button
                type="button"
                class="hover:text-zinc-300"
                onclick={() => select(link.id)}>{link.label}</button
              >
              {#if i !== 1}<span class="text-zinc-600">•</span>{/if}
            {/each}
            <span class="font-mono text-[10px] font-semibold text-zinc-300"
              >MIT License</span
            >
          </nav>
        </footer>
      </section>
    </main>
  </div>
</div>

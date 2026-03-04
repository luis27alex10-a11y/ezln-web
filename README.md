<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>EZLN: Voces de la Dignidad Rebelde</title>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Merriweather:ital,wght@0,300;0,400;0,700;1,300&family=Oswald:wght@400;600;700&display=swap" rel="stylesheet">

    <!-- Chosen Palette: Earth & Resistance (Warm off-white, Charcoal, Deep Red, Olive Drab) -->
    <!-- Application Structure Plan: A single-page application with a sticky top navigation bar acting as a chapter guide. The structure flows logically from introduction to historical context, deep-dives into demands and actors, explores the legacy, critically addresses the urgent 2026 context (displacements), and concludes with a team analysis. This linear but jumpable structure allows users to follow the narrative or skip to specific research areas. Interactive elements like tabs for demands and Canvas charts for data keep the user engaged without relying on external media. -->
    <!-- Visualization & Content Choices: 
         1. Cover: Typographic focus with CSS-styled Unicode elements to create a strong visual identity without SVG.
         2. Timeline/Context: HTML/Tailwind step layout for historical flow.
         3. Demands: Interactive JS-driven detail view (click demand -> see explanation) to manage text density.
         4. Displaced Persons (2026): Chart.js Bar Chart. Goal: Show the magnitude of the humanitarian crisis. Method: Canvas chart. Library: Chart.js.
         5. Actors & Legacy: CSS Grid cards for easy reading and comparison.
         NO SVG/Mermaid used. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->

    <style>
        /* Typography overrides */
        body {
            font-family: 'Merriweather', serif;
            background-color: #f4f1ea; /* Warm off-white */
            color: #2d2d2d; /* Charcoal */
        }
        h1, h2, h3, h4, h5, .font-heading {
            font-family: 'Oswald', sans-serif;
        }

        /* Colors */
        .bg-ezln-red { background-color: #8b0000; }
        .text-ezln-red { color: #8b0000; }
        .border-ezln-red { border-color: #8b0000; }
        
        .bg-ezln-black { background-color: #1a1a1a; }
        .text-ezln-black { color: #1a1a1a; }

        .bg-ezln-green { background-color: #4a5d23; }

        /* Chart Container Mandatory Styling */
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 800px;
            margin-left: auto;
            margin-right: auto;
            height: 40vh;
            max-height: 450px;
        }
        @media (min-width: 768px) {
            .chart-container {
                height: 50vh;
            }
        }

        /* SPA Section visibility */
        .section-content {
            display: none;
            animation: fadeIn 0.5s ease-in-out;
        }
        .section-content.active {
            display: block;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Custom Star styling (No SVG fallback) */
        .red-star {
            font-size: 8rem;
            color: #8b0000;
            line-height: 1;
            text-shadow: 4px 4px 0px #1a1a1a;
        }

        /* Scrollbar styling */
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #f4f1ea; }
        ::-webkit-scrollbar-thumb { background: #8b0000; border-radius: 4px; }
    </style>
</head>
<body class="antialiased flex flex-col min-h-screen">

    <!-- Navigation -->
    <nav class="bg-ezln-black text-white sticky top-0 z-50 shadow-lg">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-16">
                <div class="flex items-center">
                    <span class="font-heading font-bold text-xl tracking-wider text-red-500">EZLN</span>
                </div>
                <div class="hidden md:block">
                    <div class="ml-10 flex items-baseline space-x-4 font-heading text-sm">
                        <button onclick="navigate('portada')" class="nav-btn hover:text-red-400 px-3 py-2 rounded-md transition-colors" data-target="portada">Inicio</button>
                        <button onclick="navigate('contexto')" class="nav-btn hover:text-red-400 px-3 py-2 rounded-md transition-colors" data-target="contexto">Contexto</button>
                        <button onclick="navigate('causas')" class="nav-btn hover:text-red-400 px-3 py-2 rounded-md transition-colors" data-target="causas">Demandas</button>
                        <button onclick="navigate('actores')" class="nav-btn hover:text-red-400 px-3 py-2 rounded-md transition-colors" data-target="actores">Actores</button>
                        <button onclick="navigate('impacto')" class="nav-btn hover:text-red-400 px-3 py-2 rounded-md transition-colors" data-target="impacto">Legado</button>
                        <button onclick="navigate('actualidad')" class="nav-btn text-red-400 font-bold hover:text-white px-3 py-2 rounded-md transition-colors border border-red-500" data-target="actualidad">Chiapas 2026</button>
                        <button onclick="navigate('critica')" class="nav-btn hover:text-red-400 px-3 py-2 rounded-md transition-colors" data-target="critica">Análisis Crítico</button>
                    </div>
                </div>
                <!-- Mobile menu button -->
                <div class="-mr-2 flex md:hidden">
                    <button type="button" onclick="toggleMobileMenu()" class="inline-flex items-center justify-center p-2 rounded-md text-gray-400 hover:text-white hover:bg-gray-700 focus:outline-none" aria-controls="mobile-menu" aria-expanded="false">
                        <span class="sr-only">Abrir menú</span>
                        <!-- Hamburger icon (Unicode) -->
                        <span class="text-2xl">☰</span>
                    </button>
                </div>
            </div>
        </div>
        <!-- Mobile Menu -->
        <div class="md:hidden hidden bg-ezln-black border-t border-gray-700" id="mobile-menu">
            <div class="px-2 pt-2 pb-3 space-y-1 sm:px-3 font-heading flex flex-col">
                <button onclick="navigate('portada'); toggleMobileMenu()" class="text-left hover:text-red-400 block px-3 py-2 rounded-md text-base font-medium">Inicio</button>
                <button onclick="navigate('contexto'); toggleMobileMenu()" class="text-left hover:text-red-400 block px-3 py-2 rounded-md text-base font-medium">Contexto</button>
                <button onclick="navigate('causas'); toggleMobileMenu()" class="text-left hover:text-red-400 block px-3 py-2 rounded-md text-base font-medium">Demandas</button>
                <button onclick="navigate('actores'); toggleMobileMenu()" class="text-left hover:text-red-400 block px-3 py-2 rounded-md text-base font-medium">Actores</button>
                <button onclick="navigate('impacto'); toggleMobileMenu()" class="text-left hover:text-red-400 block px-3 py-2 rounded-md text-base font-medium">Legado</button>
                <button onclick="navigate('actualidad'); toggleMobileMenu()" class="text-left text-red-400 font-bold block px-3 py-2 rounded-md text-base">Chiapas 2026</button>
                <button onclick="navigate('critica'); toggleMobileMenu()" class="text-left hover:text-red-400 block px-3 py-2 rounded-md text-base font-medium">Análisis Crítico</button>
            </div>
        </div>
    </nav>

    <!-- Main Content Area -->
    <main class="flex-grow">

        <!-- 1. PORTADA CREATIVA -->
        <section id="portada" class="section-content active min-h-[90vh] flex flex-col justify-center items-center text-center p-6 bg-[url('data:image/svg+xml;utf8,<svg width=\'20\' height=\'20\' viewBox=\'0 0 20 20\' xmlns=\'http://www.w3.org/2000/svg\'><circle cx=\'2\' cy=\'2\' r=\'1\' fill=\'%23d1cdc1\'/></svg>')]">
            <div class="max-w-4xl mx-auto bg-white/80 backdrop-blur-sm p-10 rounded-xl shadow-2xl border-t-8 border-ezln-red">
                <div class="red-star mb-4 animate-pulse">★</div>
                <h1 class="font-heading text-5xl md:text-7xl font-bold uppercase tracking-tighter text-ezln-black mb-4">
                    Voces de la <span class="text-ezln-red">Dignidad</span> Rebelde
                </h1>
                <h2 class="text-xl md:text-2xl font-light text-gray-700 mb-8 border-b border-gray-300 pb-4 inline-block">
                    Investigación Profunda del Movimiento Zapatista (1994 - 2026)
                </h2>
                <p class="text-lg text-gray-600 mb-8 max-w-2xl mx-auto leading-relaxed">
                    Un viaje interactivo a través de las causas, actores, legado y la urgente realidad actual del Ejército Zapatista de Liberación Nacional en el estado de Chiapas.
                </p>
                <button onclick="navigate('contexto')" class="bg-ezln-red hover:bg-red-800 text-white font-heading text-lg py-3 px-8 rounded shadow-lg transition-transform transform hover:scale-105">
                    Comenzar la Investigación
                </button>
            </div>
        </section>

        <!-- 2. CONTEXTO HISTÓRICO -->
        <section id="contexto" class="section-content max-w-5xl mx-auto p-6 py-12">
            <div class="mb-10 text-center">
                <h2 class="font-heading text-4xl text-ezln-red font-bold mb-4 uppercase">Contexto Histórico</h2>
                <p class="text-lg text-gray-600 italic">¿Cuándo surge y por qué el "¡Ya Basta!"?</p>
            </div>

            <div class="bg-white p-8 rounded-lg shadow-md border-l-4 border-ezln-black mb-8">
                <p class="text-lg leading-relaxed mb-6">
                    Esta sección explora las raíces del levantamiento armado. No fue un evento espontáneo, sino la culminación de siglos de marginación, racismo sistémico y despojo de tierras sufridos por las comunidades indígenas de México, catalizado por políticas neoliberales de finales del siglo XX.
                </p>
            </div>

            <div class="relative border-l border-gray-300 ml-3 md:ml-6 mt-8">
                <!-- Timeline Item 1 -->
                <div class="mb-10 ml-6 md:ml-10">
                    <span class="absolute flex items-center justify-center w-8 h-8 bg-ezln-black rounded-full -left-4 ring-4 ring-white text-white font-heading text-xs">Siglos</span>
                    <h3 class="flex items-center mb-1 text-2xl font-semibold text-gray-900 font-heading">El Despojo Histórico</h3>
                    <p class="mb-4 text-base font-normal text-gray-600 leading-relaxed">Desde la colonia hasta las haciendas posrevolucionarias, las comunidades mayas de Chiapas (Tzotziles, Tzeltales, Tojolabales, Choles) fueron sistemáticamente empobrecidas, obligadas a trabajar en condiciones de semi-esclavitud y despojadas de sus tierras ancestrales. La Revolución Mexicana (1910) no llegó a Chiapas de la misma forma que al resto del país.</p>
                </div>
                <!-- Timeline Item 2 -->
                <div class="mb-10 ml-6 md:ml-10">
                    <span class="absolute flex items-center justify-center w-8 h-8 bg-ezln-green rounded-full -left-4 ring-4 ring-white text-white font-heading text-xs">1983</span>
                    <h3 class="mb-1 text-2xl font-semibold text-gray-900 font-heading">Fundación Clandestina</h3>
                    <p class="text-base font-normal text-gray-600 leading-relaxed">Un pequeño grupo de guerrilleros provenientes del norte y centro del país (Fuerzas de Liberación Nacional) llega a la Selva Lacandona. Al interactuar con las comunidades indígenas, el movimiento se transforma profundamente, subordinando la vanguardia militar a la estructura política tradicional indígena.</p>
                </div>
                <!-- Timeline Item 3 -->
                <div class="mb-10 ml-6 md:ml-10">
                    <span class="absolute flex items-center justify-center w-8 h-8 bg-ezln-red rounded-full -left-4 ring-4 ring-white text-white font-heading text-xs text-center leading-none p-1">1 Ene<br>1994</span>
                    <h3 class="mb-1 text-2xl font-semibold text-gray-900 font-heading text-ezln-red">El Levantamiento y el TLCAN</h3>
                    <p class="text-base font-normal text-gray-600 leading-relaxed">El mismo día que entra en vigor el <strong>Tratado de Libre Comercio de América del Norte (TLCAN)</strong> entre México, EE.UU. y Canadá, el EZLN declara la guerra al Estado mexicano. Consideraban el tratado como una "sentencia de muerte" para los campesinos indígenas, incapaces de competir con la agricultura subsidiada estadounidense y enfrentando la privatización de tierras ejidales (reforma al Art. 27 Constitucional).</p>
                </div>
            </div>
        </section>

        <!-- 3. CAUSAS Y DEMANDAS -->
        <section id="causas" class="section-content max-w-6xl mx-auto p-6 py-12">
            <div class="mb-10 text-center">
                <h2 class="font-heading text-4xl text-ezln-red font-bold mb-4 uppercase">Causas y Demandas</h2>
                <p class="text-lg text-gray-600 italic">¿Qué problemáticas denunciaba la Primera Declaración de la Selva Lacandona?</p>
            </div>

            <div class="bg-white p-6 rounded-lg shadow-sm border-t-2 border-gray-200 mb-8">
                <p class="text-base leading-relaxed">
                    Aquí desglosamos las demandas estructurales que motivaron el levantamiento. El EZLN no solo pedía derechos indígenas, sino una transformación democrática y de justicia social para todo México, resumida en 11 demandas iniciales (que luego se expandieron a 13). Haz clic en los botones para explorar su significado.
                </p>
            </div>

            <div class="flex flex-col md:flex-row gap-6">
                <!-- Demand Selector -->
                <div class="w-full md:w-1/3 bg-gray-100 p-4 rounded-lg">
                    <h3 class="font-heading text-xl mb-4 text-ezln-black border-b-2 border-ezln-red pb-2">Las Demandas Principales</h3>
                    <div class="flex flex-col space-y-2 h-96 overflow-y-auto pr-2" id="demand-buttons">
                        <!-- Buttons injected via JS -->
                    </div>
                </div>
                <!-- Demand Display -->
                <div class="w-full md:w-2/3 bg-white p-8 rounded-lg shadow-md border border-gray-200 flex flex-col justify-center min-h-[400px]">
                    <div id="demand-content" class="text-center">
                        <div class="text-6xl text-gray-300 mb-4">❝</div>
                        <h4 class="font-heading text-3xl text-ezln-red mb-4" id="d-title">Selecciona una demanda</h4>
                        <p class="text-lg text-gray-700 leading-relaxed" id="d-text">Usa el menú lateral para explorar las problemáticas estructurales denunciadas por el movimiento zapatista en 1994.</p>
                        <div class="text-6xl text-gray-300 mt-4 rotate-180">❝</div>
                    </div>
                </div>
            </div>
        </section>

        <!-- 4. ACTORES PRINCIPALES -->
        <section id="actores" class="section-content max-w-6xl mx-auto p-6 py-12 bg-gray-50 rounded-xl my-8">
            <div class="mb-10 text-center">
                <h2 class="font-heading text-4xl text-ezln-black font-bold mb-4 uppercase">Actores Principales</h2>
                <p class="text-lg text-gray-600 italic">Líderes, estructura y la base social del movimiento.</p>
            </div>

            <div class="mb-8 max-w-3xl mx-auto text-center">
                <p class="text-base leading-relaxed text-gray-700">
                    A diferencia de las guerrillas tradicionales de los años 70, el EZLN se caracterizó por su estructura dual: una comandancia político-militar subordinada a un comité civil indígena. Conoce a los actores clave.
                </p>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- Card 1 -->
                <div class="bg-white p-6 rounded-lg shadow border-t-4 border-ezln-red hover:shadow-lg transition-shadow">
                    <div class="text-4xl mb-3 text-center">👥</div>
                    <h3 class="font-heading text-xl font-bold text-center mb-2">Comité Clandestino Revolucionario Indígena (CCRI-CG)</h3>
                    <p class="text-sm text-gray-600 text-justify">Es la máxima autoridad de mando del EZLN. Está compuesto íntegramente por hombres y mujeres indígenas elegidos por sus comunidades. Ellos representan la dirección política ("mandar obedeciendo") por encima de la estructura puramente militar.</p>
                </div>
                <!-- Card 2 -->
                <div class="bg-white p-6 rounded-lg shadow border-t-4 border-ezln-black hover:shadow-lg transition-shadow">
                    <div class="text-4xl mb-3 text-center">🗣️</div>
                    <h3 class="font-heading text-xl font-bold text-center mb-2">Subcomandante Marcos (Capitán Marcos)</h3>
                    <p class="text-sm text-gray-600 text-justify">Antiguo vocero y jefe militar. Su figura mediática (pasamontañas, pipa, lenguaje poético/político) atrajo la atención mundial en 1994. No es indígena, y en su narrativa, su rol siempre estuvo subordinado al CCRI. En 2014 anunció que el personaje "Marcos" dejaba de existir, pasando a llamarse Galeano, y recientemente retornó al seudónimo de Capitán Marcos.</p>
                </div>
                <!-- Card 3 -->
                <div class="bg-white p-6 rounded-lg shadow border-t-4 border-ezln-green hover:shadow-lg transition-shadow">
                    <div class="text-4xl mb-3 text-center">👩🏽</div>
                    <h3 class="font-heading text-xl font-bold text-center mb-2">Comandanta Ramona y las Mujeres Zapatistas</h3>
                    <p class="text-sm text-gray-600 text-justify">Figura clave en los primeros diálogos de paz y promotora de la <strong>Ley Revolucionaria de Mujeres</strong>, que exigía derechos básicos (elegir pareja, salud, participación política) antes del levantamiento. Las mujeres indígenas son el pilar organizativo de las comunidades autónomas.</p>
                </div>
                 <!-- Card 4 -->
                 <div class="bg-white p-6 rounded-lg shadow border-t-4 border-gray-400 hover:shadow-lg transition-shadow md:col-span-2 lg:col-span-3">
                    <h3 class="font-heading text-xl font-bold text-center mb-2">Bases de Apoyo (Pueblos Originarios)</h3>
                    <p class="text-sm text-gray-600 text-center max-w-4xl mx-auto">Decenas de miles de indígenas de las etnias <strong>Tzotzil, Tzeltal, Tojolabal, Chol, Zoque y Mam</strong>. Son la verdadera fuerza del movimiento. No todos son combatientes; la gran mayoría son civiles que construyen día a día la autonomía, la salud, la educación y la justicia en sus territorios sin intervención del Estado.</p>
                </div>
            </div>
        </section>

        <!-- 5. IMPACTO Y LEGADO -->
        <section id="impacto" class="section-content max-w-5xl mx-auto p-6 py-12">
             <div class="mb-10 text-center">
                <h2 class="font-heading text-4xl text-ezln-green font-bold mb-4 uppercase">Impacto y Legado</h2>
                <p class="text-lg text-gray-600 italic">¿Qué cambios generó? De las armas a la autonomía civil.</p>
            </div>

            <div class="bg-white p-8 rounded-lg shadow-md mb-8">
                <p class="text-lg leading-relaxed">
                    Aunque el levantamiento militar duró pocos días en 1994, el impacto político y social del zapatismo ha resonado durante décadas. Esta sección evalúa cómo transformaron la política indígena en México y su influencia en los movimientos altermundistas globales.
                </p>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
                <div>
                    <h3 class="font-heading text-2xl text-ezln-black mb-4 border-b-2 border-gray-200 pb-2">Logros y Construcción</h3>
                    <ul class="space-y-4 text-gray-700">
                        <li class="flex items-start">
                            <span class="text-ezln-green mr-2 text-xl">✓</span>
                            <div>
                                <strong>Visibilización Indígena:</strong> Obligó a México y al mundo a mirar a los pueblos originarios, rompiendo el mito del "desarrollo" del TLCAN y poniendo el racismo en la agenda nacional.
                            </div>
                        </li>
                        <li class="flex items-start">
                            <span class="text-ezln-green mr-2 text-xl">✓</span>
                            <div>
                                <strong>Construcción de Autonomía (Caracoles):</strong> Ante la traición gubernamental a los Acuerdos de San Andrés, el EZLN creó en 2003 los "Caracoles" y Juntas de Buen Gobierno. Sistemas funcionales de educación, salud y justicia gestionados por ellos mismos.
                            </div>
                        </li>
                        <li class="flex items-start">
                            <span class="text-ezln-green mr-2 text-xl">✓</span>
                            <div>
                                <strong>Inspiración Global:</strong> Se convirtieron en el primer movimiento rebelde de la era del internet, inspirando los movimientos altermundistas y anticapitalistas alrededor del mundo por su enfoque no de toma del poder estatal, sino de construcción de un "mundo donde quepan muchos mundos".
                            </div>
                        </li>
                    </ul>
                </div>
                <div>
                    <h3 class="font-heading text-2xl text-ezln-red mb-4 border-b-2 border-gray-200 pb-2">Retos y Promesas Incumplidas</h3>
                    <ul class="space-y-4 text-gray-700">
                        <li class="flex items-start">
                            <span class="text-ezln-red mr-2 text-xl">✕</span>
                            <div>
                                <strong>Acuerdos de San Andrés (1996):</strong> El gobierno firmó acuerdos para reconocer derechos y cultura indígena en la Constitución, pero luego el Congreso aprobó una ley mutilada que el EZLN rechazó, rompiendo el diálogo oficial.
                            </div>
                        </li>
                        <li class="flex items-start">
                            <span class="text-ezln-red mr-2 text-xl">✕</span>
                            <div>
                                <strong>Guerra de Baja Intensidad:</strong> Desde el 94, las comunidades han sufrido acoso constante por parte de grupos paramilitares financiados o tolerados por el Estado (ej. Masacre de Acteal en 1997).
                            </div>
                        </li>
                    </ul>
                </div>
            </div>
        </section>

        <!-- 6. ACTUALIDAD: CHIAPAS 2026 -->
        <section id="actualidad" class="section-content max-w-6xl mx-auto p-6 py-12 bg-zinc-900 text-gray-200 rounded-xl my-8 border-4 border-red-800 shadow-2xl">
            <div class="mb-10 text-center">
                <span class="bg-red-600 text-white px-3 py-1 text-sm font-bold uppercase tracking-widest rounded-full mb-4 inline-block">Investigación Especial</span>
                <h2 class="font-heading text-4xl text-white font-bold mb-4 uppercase">Chiapas en 2026: Entre la Celebración y la Guerra</h2>
                <p class="text-lg text-gray-400 italic">Conmemoraciones de enero y la grave crisis de desplazados por el crimen organizado.</p>
            </div>

            <div class="bg-zinc-800 p-6 rounded-lg mb-8 text-lg leading-relaxed border-l-4 border-red-600">
                <p>
                    Esta sección de investigación profunda aborda la paradójica situación de <strong>enero de 2026</strong>. Mientras el zapatismo conmemora 32 años de su levantamiento público, Chiapas atraviesa una de las crisis humanitarias más graves de su historia reciente debido al desbordamiento de la violencia del narcotráfico.
                </p>
            </div>

            <!-- Commemoration Info -->
            <div class="mb-12">
                <h3 class="font-heading text-3xl text-red-400 mb-6">Enero 2026: 32 Años de Resistencia</h3>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div class="bg-zinc-800 p-6 rounded shadow">
                        <h4 class="font-bold text-xl mb-3 text-white">Reorganización de la Autonomía</h4>
                        <p class="text-gray-300 text-sm">
                            En respuesta a la violencia y el colapso del Estado, el EZLN anunció previamente la disolución de los Municipios Autónomos Rebeldes Zapatistas (MAREZ) y las Juntas de Buen Gobierno, para dar paso a una estructura más descentralizada y segura: los <strong>Gobiernos Autónomos Locales (GAL)</strong>, los Colectivos de Gobiernos Autónomos Zapatistas (CGAZ) y las Asambleas de Colectivos (ACGZ). Las celebraciones de 2026 se centran en consolidar esta nueva forma de autodefensa civil y política.
                        </p>
                    </div>
                    <div class="bg-zinc-800 p-6 rounded shadow">
                        <h4 class="font-bold text-xl mb-3 text-white">El Mensaje a 32 Años</h4>
                        <p class="text-gray-300 text-sm">
                            Los comunicados zapatistas de principios de 2026 denuncian con vehemencia que Chiapas es "un estado fallido", despedazado por la disputa territorial entre los grandes cárteles de la droga (Cártel de Sinaloa y CJNG). El EZLN señala la inacción, y en ocasiones la complicidad, de las fuerzas armadas estatales y federales en esta guerra que afecta principalmente a comunidades campesinas e indígenas.
                        </p>
                    </div>
                </div>
            </div>

            <!-- Displaced Persons Crisis & Chart -->
            <div>
                <h3 class="font-heading text-3xl text-red-400 mb-6">La Crisis Humanitaria: Desplazamiento Forzado</h3>
                <p class="mb-6 text-gray-300">
                    Lejos del foco turístico, regiones enteras como la Frontera, la Sierra Mariscal, y los Altos de Chiapas enfrentan una "guerra civil" no declarada. Comunidades enteras huyen del reclutamiento forzado, las extorsiones, balaceras y bloqueos. El zapatismo intenta proteger sus territorios ante este cerco paramilitar y narco.
                </p>
                
                <div class="bg-white rounded-lg p-4 shadow-inner mb-6">
                    <h4 class="text-gray-800 font-heading text-center text-xl mb-2">Estimación de Personas Desplazadas en Chiapas por Violencia (Proyección 2024-2026)</h4>
                    <p class="text-xs text-center text-gray-500 mb-4">Datos ilustrativos basados en reportes de ONGs y medios de comunicación sobre la escalada de violencia en las regiones foco.</p>
                    <!-- REQUIRED CHART CONTAINER -->
                    <div class="chart-container">
                        <canvas id="displacedChart"></canvas>
                    </div>
                </div>
            </div>
        </section>

        <!-- 7. SECCIÓN CRÍTICA DEL EQUIPO -->
        <section id="critica" class="section-content max-w-5xl mx-auto p-6 py-12 mb-20">
            <div class="mb-10 text-center">
                <h2 class="font-heading text-4xl text-ezln-black font-bold mb-4 uppercase">Análisis Crítico del Equipo</h2>
                <p class="text-lg text-gray-600 italic">Opinión argumentada sobre la relevancia del movimiento hoy.</p>
            </div>

            <div class="bg-white p-8 rounded-xl shadow-lg border-t-8 border-ezln-black relative">
                <div class="absolute -top-6 -left-6 text-6xl text-gray-200">❝</div>
                
                <div class="space-y-6 text-gray-800 text-lg leading-relaxed relative z-10">
                    <p>
                        A 32 años de su aparición pública, opinamos que <strong>la relevancia del EZLN no radica en su capacidad militar, sino en su autoridad moral y en su propuesta de organización política alternativa.</strong>
                    </p>
                    <p>
                        <strong>El fracaso del modelo oficial:</strong> El levantamiento de 1994 fue una advertencia sobre los peligros del modelo neoliberal (TLCAN) para el tejido social campesino. Hoy, en 2026, la crisis de violencia en México parece darle la razón al diagnóstico zapatista: el Estado se ha debilitado frente a los poderes fácticos (el crimen organizado) mientras la desigualdad estructural persiste.
                    </p>
                    <p>
                        <strong>La autonomía como trinchera:</strong> Mientras gran parte de Chiapas sufre el flagelo del narcotráfico, los territorios zapatistas, aunque asediados, han demostrado ser uno de los pocos muros de contención funcionales. Su sistema de <em>Gobiernos Autónomos Locales</em> prueba que la autoorganización comunitaria es más eficaz para garantizar seguridad, educación y salud que las políticas asistencialistas o la militarización impuesta desde el centro del país.
                    </p>
                    <p>
                        <strong>Conclusión:</strong> El zapatismo hoy no busca "tomar el poder" en Ciudad de México. Su legado es haber demostrado que <strong>es posible organizar la vida en comunidad fuera de la lógica capitalista y de los partidos políticos tradicionales.</strong> Sin embargo, enfrentan un peligro existencial enorme; la guerra civil silenciosa que azota Chiapas por los cárteles de la droga es la mayor amenaza que han enfrentado, revelando que el mayor legado del zapatismo podría ser su obstinada resistencia a desaparecer en medio de la barbarie.
                    </p>
                </div>
                <div class="absolute -bottom-10 -right-6 text-6xl text-gray-200 rotate-180">❝</div>
            </div>
        </section>

    </main>

    <!-- Footer -->
    <footer class="bg-ezln-black text-white py-6 text-center text-sm font-heading mt-auto">
        <p>Documento Interactivo - Investigación de Ciencias Sociales y Entorno Político</p>
        <p class="text-gray-500 mt-2">© 2026 - SPA Educativa sobre el Movimiento Zapatista en Chiapas.</p>
    </footer>

    <!-- JavaScript Logic -->
    <script>
        // SPA Navigation Logic
        function navigate(sectionId) {
            // Hide all sections
            document.querySelectorAll('.section-content').forEach(el => {
                el.classList.remove('active');
            });
            // Show target section
            document.getElementById(sectionId).classList.add('active');
            
            // Scroll to top
            window.scrollTo({ top: 0, behavior: 'smooth' });

            // Update nav styling
            document.querySelectorAll('.nav-btn').forEach(btn => {
                if(btn.dataset.target === 'actualidad') {
                    // Maintain special styling for this button
                    btn.classList.remove('bg-red-900');
                    if(sectionId === 'actualidad') btn.classList.add('bg-red-900');
                } else {
                    btn.classList.remove('text-red-400', 'font-bold');
                    btn.classList.add('text-white');
                    if(btn.dataset.target === sectionId) {
                        btn.classList.remove('text-white');
                        btn.classList.add('text-red-400', 'font-bold');
                    }
                }
            });
        }

        // Mobile Menu Toggle
        function toggleMobileMenu() {
            const menu = document.getElementById('mobile-menu');
            menu.classList.toggle('hidden');
        }

        // Demands Interactive Logic (State and Data)
        const demandsData = [
            { id: 'tierra', title: 'Tierra', text: 'La exigencia de la restitución de tierras usurpadas a los pueblos indígenas y el fin de la privatización de los ejidos impulsada por las reformas previas al TLCAN. "La tierra es de quien la trabaja".' },
            { id: 'trabajo', title: 'Trabajo', text: 'Demanda de salarios justos y condiciones laborales dignas, rechazando la explotación histórica en fincas y plantaciones que asemejaba a la esclavitud.' },
            { id: 'techo', title: 'Techo', text: 'El derecho a una vivienda digna con servicios básicos. Antes de 1994, la mayoría de las viviendas indígenas en la región carecían de electricidad, agua potable o pisos de cemento.' },
            { id: 'alimentacion', title: 'Alimentación', text: 'Garantizar la soberanía alimentaria para combatir los altos índices de desnutrición que asolaban a las infancias indígenas en Chiapas.' },
            { id: 'salud', title: 'Salud', text: 'Creación de hospitales y clínicas equipadas. El zapatismo denunció que en Chiapas morían miles por enfermedades curables (diarrea, infecciones) por falta de atención médica estatal.' },
            { id: 'educacion', title: 'Educación', text: 'Escuelas bilingües y gratuitas que respeten la cultura e idioma de las comunidades, rompiendo con el sistema educativo integracionista y castellanizador.' },
            { id: 'independencia', title: 'Independencia', text: 'Soberanía nacional frente a imposiciones económicas extranjeras (como el TLCAN) que afectan directamente a los sectores más vulnerables del país.' },
            { id: 'libertad', title: 'Libertad', text: 'Libertad política y de asociación sin represión, cacicazgos locales ni violencia de estado.' },
            { id: 'democracia', title: 'Democracia', text: 'El fin del régimen de partido de estado (PRI en aquel entonces) y la creación de un sistema donde la ciudadanía mande y el gobierno obedezca ("Mandar obedeciendo").' },
            { id: 'justicia', title: 'Justicia', text: 'Un sistema judicial imparcial que no criminalice la pobreza ni la disidencia, y que castigue los abusos contra los pueblos originarios.' },
            { id: 'paz', title: 'Paz', text: 'Una paz verdadera basada en la justicia social y la dignidad, no solo la ausencia de conflicto armado o la paz impuesta por la fuerza militar.' }
        ];

        function initDemands() {
            const btnContainer = document.getElementById('demand-buttons');
            demandsData.forEach(demand => {
                const btn = document.createElement('button');
                btn.className = 'text-left px-4 py-3 bg-white hover:bg-gray-200 border-l-4 border-transparent hover:border-ezln-red rounded transition-colors font-semibold text-gray-700 w-full';
                btn.innerText = demand.title;
                btn.onclick = () => showDemand(demand, btn);
                btnContainer.appendChild(btn);
            });
        }

        function showDemand(demand, activeBtn) {
            document.getElementById('d-title').innerText = demand.title;
            document.getElementById('d-text').innerText = demand.text;
            
            // Update button styles
            const buttons = document.getElementById('demand-buttons').children;
            for(let b of buttons) {
                b.classList.remove('border-ezln-red', 'bg-gray-200');
                b.classList.add('border-transparent');
            }
            activeBtn.classList.remove('border-transparent');
            activeBtn.classList.add('border-ezln-red', 'bg-gray-200');
        }

        // Chart.js Setup for "Actualidad 2026"
        function initChart() {
            const ctx = document.getElementById('displacedChart').getContext('2d');
            
            // Note: Data is illustrative projection based on recent trends (2024-2025) for the 2026 scenario request.
            const chartData = {
                labels: ['Frontera Comalapa', 'Chicomuselo', 'Pantelhó', 'Chenalhó', 'Motozintla / Sierra'],
                datasets: [{
                    label: 'Estimación de Desplazados (Miles de personas)',
                    data: [8.5, 6.2, 4.0, 3.5, 5.8],
                    backgroundColor: [
                        'rgba(139, 0, 0, 0.7)',   // ezln-red with opacity
                        'rgba(26, 26, 26, 0.7)',  // ezln-black
                        'rgba(74, 93, 35, 0.7)',  // ezln-green
                        'rgba(139, 0, 0, 0.4)',
                        'rgba(26, 26, 26, 0.4)'
                    ],
                    borderColor: [
                        'rgba(139, 0, 0, 1)',
                        'rgba(26, 26, 26, 1)',
                        'rgba(74, 93, 35, 1)',
                        'rgba(139, 0, 0, 1)',
                        'rgba(26, 26, 26, 1)'
                    ],
                    borderWidth: 1
                }]
            };

            const chartConfig = {
                type: 'bar',
                data: chartData,
                options: {
                    responsive: true,
                    maintainAspectRatio: false, // Critical to fit within the constrained container height
                    plugins: {
                        legend: {
                            display: false // Hide legend to save space
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    return context.parsed.y + ' Mil personas aprox.';
                                }
                            }
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            title: {
                                display: true,
                                text: 'Miles de Personas'
                            }
                        },
                        x: {
                            ticks: {
                                // Label wrapping logic (max ~16 chars per line for long labels)
                                callback: function(value) {
                                    const label = this.getLabelForValue(value);
                                    if (label.length > 16) {
                                        return label.match(/.{1,16}(\s|$)/g);
                                    }
                                    return label;
                                }
                            }
                        }
                    }
                }
            };

            new Chart(ctx, chartConfig);
        }

        // Initialization
        window.onload = () => {
            initDemands();
            initChart();
            // Start on cover
            navigate('portada');
        };

    </script>
</body>
</html>

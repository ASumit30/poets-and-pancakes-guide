<!DOCTYPE html>
<html lang="en" class="scroll-smooth">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>An Interactive Guide to Poets and Pancakes</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;700&family=Roboto:wght@300;400;700&display=swap" rel="stylesheet">
    <!-- Chosen Palette: Classic Cinema -->
    <!-- Application Structure Plan: The SPA is designed as an interactive dashboard, diverging from the linear report structure for better usability. A sticky navigation bar (The Studio, The Cast, The Themes, The Incidents) allows non-linear exploration. The 'Cast' section uses interactive cards and a radar chart for character comparison, making abstract traits tangible. The 'Themes' section employs an accordion to present dense information in a digestible, user-controlled manner. The 'Incidents' section uses a 'reveal' interaction to mirror the story's own mysterious narrative arc. This structure was chosen to transform passive reading into active exploration, making the literary analysis more engaging and memorable for a student audience. Sections for LLM-powered Q&A and character dialogue are integrated for deeper, personalized textual inquiry. The key change is a "page-by-page" simulation via JavaScript, where clicking a nav link hides all other main sections and displays the selected one, making each section feel like a distinct page. -->
    <!-- Visualization & Content Choices: 
        1. Report Info: Character traits (Subbu, Office Boy, etc.). Goal: Compare personalities. Viz/Method: Interactive character cards + Radar Chart. Interaction: Click cards to show details; chart provides at-a-glance comparison. Justification: A radar chart visually quantifies and contrasts qualitative traits, offering a unique analytical perspective. Library: Chart.js (Canvas).
        2. Report Info: Diverse makeup department staff. Goal: Showcase National Integration. Viz/Method: HTML/CSS diagram. Interaction: Static visual. Justification: A simple, effective visual metaphor for a key concept, built without graphics libraries.
        3. Report Info: Thematic analysis (Art vs. Commerce, etc.). Goal: Organize dense text. Viz/Method: Accordion component. Interaction: Click to expand/collapse. Justification: Prevents a 'wall of text,' improving readability and user focus. Library/Method: Vanilla JS.
        4. Report Info: Stephen Spender's mysterious visit. Goal: Recreate the narrative's sense of mystery and revelation. Viz/Method: Two-state panel. Interaction: Button click to 'solve the mystery'. Justification: Turns a story point into an interactive discovery, enhancing engagement. Library/Method: Vanilla JS.
        5. Report Info: General textual understanding. Goal: Allow free-form Q&A. Viz/Method: Text input and display area. Interaction: User types question, AI generates answer. Justification: Provides personalized learning and addresses specific user curiosities. Library: Gemini API (LLM).
        6. Report Info: Deeper thematic understanding. Goal: Provide extended explanation for themes. Viz/Method: Button within accordion. Interaction: Click button, AI expands on theme. Justification: Offers on-demand detailed analysis, preventing information overload initially. Library: Gemini API (LLM).
        7. Report Info: Character perspectives and voices. Goal: Simulate dialogue with characters. Viz/Method: Character selection dropdown, text input, response display. Interaction: User selects character, types message, AI generates in-character response. Justification: Enhances character empathy and understanding by allowing direct, persona-driven interaction. Library: Gemini API (LLM).
        8. Report Info: Section Visuals. Goal: Enhance attractiveness. Viz/Method: Placehold.co images. Interaction: Static. Justification: Adds visual interest and breaks up text, adhering to "no SVG" rule. Library: HTML.
        -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body {
            font-family: 'Roboto', sans-serif;
            background-color: #FDF8F0;
            color: #433A3F;
        }
        h1, h2, h3 {
            font-family: 'Playfair Display', serif;
        }
        .nav-link {
            transition: color 0.3s, border-bottom-color 0.3s;
            border-bottom: 2px solid transparent;
        }
        .nav-link:hover, .nav-link.active {
            color: #D36A5D;
            border-bottom-color: #D36A5D;
        }
        .hero-section {
            background-image: linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)), url('https://placehold.co/1200x600/8d8d8d/fdf8f0?text=Gemini+Studios+1950s');
            background-size: cover;
            background-position: center;
        }
        .accordion-header {
            transition: background-color 0.3s;
        }
        .accordion-header.open, .accordion-header:hover {
            background-color: #EAE0D5;
        }
        .accordion-content {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.5s ease-in-out, padding 0.5s ease-in-out;
        }
        .chart-container { 
            position: relative; 
            width: 100%; 
            max-width: 500px; 
            margin-left: auto; 
            margin-right: auto; 
            height: 350px;
            max-height: 400px;
        }
        @media (min-width: 768px) { 
            .chart-container { 
                height: 400px; 
            } 
        }
        .loading-spinner {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #D36A5D;
            border-radius: 50%;
            width: 20px;
            height: 20px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .content-section {
            display: none; /* Hidden by default */
        }
        .content-section.active {
            display: block; /* Shown when active */
        }
    </style>
</head>
<body class="antialiased">

    <!-- Header & Navigation -->
    <header id="header" class="bg-[#FDF8F0]/80 backdrop-blur-sm sticky top-0 z-50 shadow-md">
        <nav class="container mx-auto px-6 py-3 flex justify-between items-center">
            <h1 class="text-xl md:text-2xl font-bold text-[#433A3F]">Poets & Pancakes</h1>
            <div class="hidden md:flex space-x-8">
                <a href="#studio" class="nav-link active">The Studio</a>
                <a href="#cast" class="nav-link">The Cast</a>
                <a href="#themes" class="nav-link">The Themes</a>
                <a href="#incidents" class="nav-link">The Incidents</a>
                <a href="#ask-narrator" class="nav-link">Ask the Narrator ✨</a>
                <a href="#character-dialogue" class="nav-link">Character Dialogue ✨</a>
            </div>
            <button id="mobile-menu-button" class="md:hidden text-2xl">☰</button>
        </nav>
        <div id="mobile-menu" class="hidden md:hidden px-6 pb-4 flex flex-col space-y-2">
            <a href="#studio" class="nav-link text-center py-1 active">The Studio</a>
            <a href="#cast" class="nav-link text-center py-1">The Cast</a>
            <a href="#themes" class="nav-link text-center py-1">The Themes</a>
            <a href="#incidents" class="nav-link text-center py-1">The Incidents</a>
            <a href="#ask-narrator" class="nav-link text-center py-1">Ask the Narrator ✨</a>
            <a href="#character-dialogue" class="nav-link text-center py-1">Character Dialogue ✨</a>
        </div>
    </header>

    <main>
        <!-- Hero Section -->
        <section class="hero-section h-[60vh] flex items-center justify-center text-center text-white p-4">
            <div class="bg-black/30 p-8 rounded-lg">
                <h1 class="text-4xl md:text-6xl font-bold mb-4">Inside Gemini Studios</h1>
                <p class="text-lg md:text-xl max-w-3xl mx-auto">An interactive exploration of Asokamitran's "Poets and Pancakes," a witty and insightful look into the world of Indian cinema during its golden age.</p>
            </div>
        </section>

        <!-- The Studio Section -->
        <section id="studio" class="content-section py-16 md:py-24 bg-[#EAE0D5] active">
            <div class="container mx-auto px-6">
                <h2 class="text-3xl md:text-4xl font-bold text-center mb-4">The Studio: A World Within</h2>
                <p class="text-center max-w-3xl mx-auto mb-12 text-lg">
                    Gemini Studios was more than just a film production house; it was a microcosm of post-colonial India. This section delves into the studio's unique environment, from its infamous makeup department and bustling daily life to its unexpected role as a symbol of national unity.
                </p>
                
                <div class="grid md:grid-cols-2 gap-12 items-start mb-12">
                    <div class="text-content">
                        <h3 class="text-2xl font-bold mb-3">The Fiery Misery of Makeup 💄</h3>
                        <p class="mb-4">The makeup room, strategically located in what were once Robert Clive’s stables, was a realm of "fiery misery." Dozens of incandescent lights, positioned at all angles around half a dozen large mirrors, generated oppressive heat. Actors were subjected to copious amounts of "Pancake" makeup—a popular brand purchased in "truckloads" by the studio. This thick, sticky substance was applied generously, often transforming any decent-looking individual into a "hideous crimson hued monster." This elaborate, uncomfortable process epitomized the artificiality and illusion inherent in the film industry, highlighting the stark contrast between the glamorous on-screen persona and the arduous reality of its creation.</p>
                        <p>The hierarchy within the makeup department was rigid: the chief makeup man handled the main stars, his senior assistant the secondary leads, the junior assistant the comedians, and the "office boy" was responsible for the crowd actors. This meticulous, almost assembly-line approach to cosmetic transformation underscored the industrial nature of filmmaking, where individual features were subsumed by the need for a uniform, camera-ready appearance.</p>
                    </div>
                    <div>
                        <img src="https://placehold.co/600x400/D36A5D/FDF8F0?text=Makeup+Room+Chaos" alt="Illustrative image of a chaotic makeup room with bright lights." class="w-full h-auto rounded-lg shadow-lg">
                    </div>
                </div>

                <div class="grid md:grid-cols-2 gap-12 items-start mb-12">
                    <div>
                        <img src="https://placehold.co/600x400/433A3F/FDF8F0?text=Cultural+Diversity+in+Studio" alt="Illustrative image of diverse people working together in a studio setting." class="w-full h-auto rounded-lg shadow-lg">
                    </div>
                    <div class="text-content">
                        <h3 class="text-2xl font-bold mb-3">A Crucible of National Integration 🤝</h3>
                        <p class="mb-4">Remarkably, the makeup department at Gemini Studios served as an unwitting example of spontaneous national integration. The department was initially headed by a Bengali, succeeded by a Maharashtrian, and staffed by assistants from Andhra, Karnataka, and local Tamils. This diverse composition meant that individuals from various linguistic and cultural backgrounds worked side-by-side, united by their shared professional goals. This organic unity, born from daily collaboration and mutual dependence, offered a compelling contrast to the more deliberate and often forced attempts at national integration seen in government initiatives like All India Radio or Doordarshan.</p>
                        <p>The studio, therefore, was not just a workplace but a unique social melting pot where regional differences seemed to dissolve in the shared pursuit of cinematic creation. It demonstrated that unity could naturally emerge from collaborative work, fostering a sense of shared identity in a newly independent nation striving to define itself.</p>
                        <div class="flex flex-col items-center justify-center p-8 bg-[#FDF8F0] rounded-lg shadow-inner mt-6">
                            <h3 class="text-xl font-bold mb-4">Integration in Action</h3>
                            <div class="w-full text-center">
                                <div class="flex justify-around mb-2">
                                    <span class="bg-[#B0A9A3] p-2 rounded-md shadow-sm">Bengal</span>
                                    <span class="bg-[#B0A9A3] p-2 rounded-md shadow-sm">Maharashtra</span>
                                </div>
                                <div class="flex justify-center my-4">
                                    <span class="text-4xl text-[#D36A5D]">↓</span>
                                </div>
                                <div class="p-4 bg-[#D36A5D] text-white rounded-lg shadow-inner">
                                    <h4 class="font-bold">Gemini Makeup Dept.</h4>
                                    <p class="text-sm">(Chennai)</p>
                                </div>
                                <div class="flex justify-center my-4">
                                    <span class="text-4xl text-[#D36A5D]">↑</span>
                                </div>
                                <div class="flex justify-around">
                                    <span class="bg-[#B0A9A3] p-2 rounded-md shadow-sm">Andhra</span>
                                    <span class="bg-[#B0A9A3] p-2 rounded-md shadow-sm">Karnataka</span>
                                    <span class="bg-[#B0A9A3] p-2 rounded-md shadow-sm">Tamil</span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="grid md:grid-cols-2 gap-12 items-start">
                    <div class="text-content">
                        <h3 class="text-2xl font-bold mb-3">The Ever-Buzzing Atmosphere 🎬</h3>
                        <p class="mb-4">Beyond the makeup room, Gemini Studios vibrated with a constant, almost overwhelming energy. Over 600 staff members bustled about, contributing to the creation of Tamil, Hindi, and Telugu films. The air was thick with the scent of "Pancake," the murmur of dialogue, the clatter of equipment, and the incessant hum of activity. Gossip was a rampant currency, flowing freely through every department, fueled by both genuine camaraderie and underlying jealousies. The legal department's isolated cubicle, for instance, became a refuge for the Office Boy to vent his frustrations, highlighting the informal communication networks that thrived amidst the formal structures.</p>
                        <p>This dynamic environment, a blend of creative chaos and strict hierarchy, reflected the nascent yet powerful Indian film industry finding its feet in the post-independence era. It was a place where dreams were both forged and shattered, where the pursuit of art was inextricably linked with commercial enterprise, and where every individual, no matter their role, played a part in the grand illusion of cinema.</p>
                    </div>
                    <div>
                        <img src="https://placehold.co/600x400/B0A9A3/433A3F?text=Studio+Daily+Life" alt="Illustrative image of a film studio set with lights and crew." class="w-full h-auto rounded-lg shadow-lg">
                    </div>
                </div>
            </div>
        </section>

        <!-- The Cast Section -->
        <section id="cast" class="content-section py-16 md:py-24">
            <div class="container mx-auto px-6">
                <h2 class="text-3xl md:text-4xl font-bold text-center mb-4">The Cast of Characters</h2>
                <p class="text-center max-w-3xl mx-auto mb-12 text-lg">
                    Gemini Studios was less a place of business and more a vibrant tapestry woven from its eccentric, ambitious, and often frustrated personalities. This section invites you to meet the key players whose lives and quirks shaped the studio's unique identity. Click on a character's card to delve into their detailed profile and symbolic significance within the story, then observe their contrasting traits in the radar chart below.
                </p>
                <div class="grid md:grid-cols-2 gap-12">
                    <div class="flex flex-col space-y-4">
                        <button class="character-btn text-left p-4 bg-[#EAE0D5] rounded-lg shadow-md transition hover:bg-[#D36A5D] hover:text-white" data-character="subbu">
                            <h3 class="text-xl font-bold">Kothamangalam Subbu</h3>
                            <p class="text-sm">The Versatile Genius (No. 2) ✨</p>
                        </button>
                        <button class="character-btn text-left p-4 bg-[#EAE0D5] rounded-lg shadow-md transition hover:bg-[#D36A5D] hover:text-white" data-character="officeBoy">
                            <h3 class="text-xl font-bold">The Office Boy</h3>
                            <p class="text-sm">The Frustrated Aspirant 😠</p>
                        </button>
                        <button class="character-btn text-left p-4 bg-[#EAE0D5] rounded-lg shadow-md transition hover:bg-[#D36A5D] hover:text-white" data-character="lawyer">
                            <h3 class="text-xl font-bold">The Lawyer</h3>
                            <p class="text-sm">The Man of Cold Logic ⚖️</p>
                        </button>
                        <button class="character-btn text-left p-4 bg-[#EAE0D5] rounded-lg shadow-md transition hover:bg-[#D36A5D] hover:text-white" data-character="asokamitran">
                            <h3 class="text-xl font-bold">Asokamitran</h3>
                            <p class="text-sm">The Quiet Observer 👁️</p>
                        </button>
                    </div>
                    <div id="character-display" class="p-6 bg-[#EAE0D5] rounded-lg shadow-inner min-h-[200px] flex flex-col justify-center items-center text-center">
                        <h3 id="character-name" class="text-2xl font-bold mb-2">Select a Character</h3>
                        <p id="character-description" class="text-gray-700">Click on a name to the left to see their profile and symbolic significance within the story. Each character offers a unique perspective on the bustling world of Gemini Studios.</p>
                    </div>
                </div>
                <div class="mt-16">
                    <h3 class="text-2xl md:text-3xl font-bold text-center mb-8">A Visual Comparison of Personalities</h3>
                     <div class="chart-container">
                        <canvas id="characterChart"></canvas>
                    </div>
                </div>
            </div>
        </section>

        <!-- The Themes Section -->
        <section id="themes" class="content-section py-16 md:py-24 bg-[#EAE0D5]">
            <div class="container mx-auto px-6">
                <h2 class="text-3xl md:text-4xl font-bold text-center mb-4">Unpacking the Themes</h2>
                 <p class="text-center max-w-3xl mx-auto mb-12 text-lg">
                    "Poets and Pancakes" transcends mere anecdote, offering sharp social commentary through its rich thematic layers. This section provides an interactive way to explore the story's major themes, from the clash between art and commerce to the subtle workings of satire. Click each theme to reveal a comprehensive analysis, and for even deeper insights, use the "Deep Dive" button powered by our AI assistant!
                </p>
                <div id="accordion-container" class="max-w-4xl mx-auto space-y-2">
                    <!-- Accordion items will be inserted here by JS -->
                </div>
            </div>
        </section>

        <!-- The Incidents Section -->
        <section id="incidents" class="content-section py-16 md:py-24">
            <div class="container mx-auto px-6">
                <h2 class="text-3xl md:text-4xl font-bold text-center mb-4">Pivotal Incidents</h2>
                 <p class="text-center max-w-3xl mx-auto mb-12 text-lg">
                    Two significant visits left a lasting, if initially confusing, impression on Gemini Studios. These events highlight the studio's ideological leanings and the cultural disconnects of the era. Explore the strange visit of an English poet below, and unravel the mystery that baffled the studio for years.
                </p>
                <div class="grid md:grid-cols-2 gap-12">
                    <div class="p-8 bg-[#EAE0D5] rounded-lg shadow-lg">
                         <h3 class="text-2xl font-bold mb-3">Visit 1: The MRA (Moral Re-Armament Army) 🎭</h3>
                         <p class="mb-4">In 1952, Gemini Studios extended a warm welcome to the Moral Re-Armament (MRA) Army, a group of about two hundred international actors and actresses. This anti-communist movement was ironically seen by some as an "international circus," yet their two plays, "Jotham Valley" and "The Forgotten Factor," were staged with remarkable professionalism. Despite their simple messages, their production quality profoundly impressed the local Tamil drama community, influencing their craft for years. This visit subtly underscored the studio's anti-communist stance, aligning it with a global ideological current.</p>
                         <img src="https://placehold.co/600x300/A0887D/FDF8F0?text=MRA+Performance" alt="Illustrative image of a stage performance." class="w-full h-auto rounded-lg shadow-md mt-4">
                    </div>
                    <div id="spender-mystery" class="p-8 bg-[#EAE0D5] rounded-lg shadow-lg flex flex-col items-center justify-center text-center">
                        <h3 class="text-2xl font-bold mb-3">Visit 2: The English Poet's Mystery 🤔</h3>
                        <div id="mystery-initial">
                            <p class="text-5xl mb-4">❓</p>
                            <p class="mb-4">A few months after the MRA, another mysterious visitor arrived: an English poet. His accent was so dense that his speech about being a poet or an editor left the entire studio audience utterly baffled. No one could understand the purpose of his visit or what he was trying to convey. For years, this perplexing incident remained an unsolved puzzle, a curious footnote in the studio's history.</p>
                            <button id="solve-mystery-btn" class="px-4 py-2 bg-[#D36A5D] text-white rounded-lg hover:bg-[#b9554a] transition">Solve the Mystery</button>
                        </div>
                        <div id="mystery-solved" class="hidden">
                            <p class="text-5xl mb-4">💡</p>
                            <p class="mb-4">Years later, the narrator, Asokamitran, finally pieced together the puzzle. While outside Gemini Studios, he discovered a paperback titled <span class="font-italic font-bold">"The God That Failed."</span> It was a collection of essays by six eminent writers, including the elusive visitor, <span class="font-bold">Stephen Spender</span>. The book revealed their disillusionment with Communism. Spender's visit was thus part of the same broader anti-communist wave that saw the MRA welcomed at Gemini Studios. The mystery was solved, revealing how global ideological battles played out in the most unlikely of local settings.</p>
                            <img src="https://placehold.co/400x200/433A3F/FDF8F0?text=The+God+That+Failed" alt="Illustrative image of a book cover titled 'The God That Failed'." class="w-auto h-auto rounded-lg shadow-md mt-4">
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- Ask the Narrator Section (LLM Powered) -->
        <section id="ask-narrator" class="content-section py-16 md:py-24 bg-[#FDF8F0]">
            <div class="container mx-auto px-6 max-w-3xl">
                <h2 class="text-3xl md:text-4xl font-bold text-center mb-4">Ask the Narrator ✨</h2>
                <p class="text-center mb-12 text-lg">
                    Have a specific question about "Poets and Pancakes"? Ask our AI-powered narrator anything you want to know about the characters, themes, incidents, or context of the story!
                </p>
                <div class="bg-[#EAE0D5] p-8 rounded-lg shadow-lg">
                    <textarea id="narrator-question" class="w-full p-3 rounded-md border-gray-300 focus:outline-none focus:ring-2 focus:ring-[#D36A5D] mb-4 h-32 resize-y" placeholder="e.g., What was the significance of Pancake makeup in the story?"></textarea>
                    <button id="ask-narrator-btn" class="w-full px-4 py-2 bg-[#D36A5D] text-white rounded-lg hover:bg-[#b9554a] transition flex items-center justify-center">
                        <span id="ask-narrator-text">Ask the Narrator ✨</span>
                        <div id="ask-narrator-loading" class="loading-spinner hidden ml-2"></div>
                    </button>
                    <div id="narrator-response-container" class="mt-6 p-4 bg-white rounded-md shadow-inner hidden">
                        <h3 class="font-bold text-xl mb-2">Narrator's Response:</h3>
                        <p id="narrator-response" class="text-gray-700"></p>
                    </div>
                </div>
            </div>
        </section>

        <!-- Character Dialogue Section (LLM Powered) -->
        <section id="character-dialogue" class="content-section py-16 md:py-24">
            <div class="container mx-auto px-6 max-w-3xl">
                <h2 class="text-3xl md:text-4xl font-bold text-center mb-4">Character Dialogue Simulator ✨</h2>
                <p class="text-center mb-12 text-lg">
                    Ever wondered what it would be like to talk to Kothamangalam Subbu, or perhaps get some advice from the Office Boy? Choose a character and engage in a simulated conversation! The AI will respond in their unique persona.
                </p>
                <div class="bg-[#EAE0D5] p-8 rounded-lg shadow-lg">
                    <div class="mb-4">
                        <label for="character-select" class="block text-lg font-bold mb-2">Choose a Character:</label>
                        <select id="character-select" class="w-full p-3 rounded-md border-gray-300 focus:outline-none focus:ring-2 focus:ring-[#D36A5D]">
                            <option value="subbu">Kothamangalam Subbu</option>
                            <option value="officeBoy">The Office Boy</option>
                            <option value="lawyer">The Lawyer</option>
                            <option value="asokamitran">Asokamitran (The Narrator)</option>
                        </select>
                    </div>
                    <textarea id="user-dialogue-input" class="w-full p-3 rounded-md border-gray-300 focus:outline-none focus:ring-2 focus:ring-[#D36A5D] mb-4 h-32 resize-y" placeholder="What would you like to ask or say to this character?"></textarea>
                    <button id="send-dialogue-btn" class="w-full px-4 py-2 bg-[#D36A5D] text-white rounded-lg hover:bg-[#b9554a] transition flex items-center justify-center">
                        <span id="send-dialogue-text">Send Message ✨</span>
                        <div id="send-dialogue-loading" class="loading-spinner hidden ml-2"></div>
                    </button>
                    <div id="character-dialogue-response-container" class="mt-6 p-4 bg-white rounded-md shadow-inner hidden">
                        <h3 class="font-bold text-xl mb-2" id="dialogue-response-heading">Character's Response:</h3>
                        <p id="character-dialogue-response" class="text-gray-700"></p>
                    </div>
                </div>
            </div>
        </section>

    </main>

    <footer class="bg-[#433A3F] text-[#FDF8F0] py-8 text-center">
        <p>An Interactive Guide based on Asokamitran's "Poets and Pancakes".</p>
        <p class="text-sm text-[#B0A9A3] mt-2">Designed for educational exploration.</p>
        <p class="text-sm text-[#B0A9A3] mt-2">Made by A. Sumit Ranjan</p>
        <p class="text-sm text-[#B0A9A3]">AI Features powered by Gemini 🌟</p>
    </footer>

    <script>
        document.addEventListener('DOMContentLoaded', () => {

            // Mobile Menu Toggle
            const mobileMenuButton = document.getElementById('mobile-menu-button');
            const mobileMenu = document.getElementById('mobile-menu');
            mobileMenuButton.addEventListener('click', () => {
                mobileMenu.classList.toggle('hidden');
            });

            // Page Navigation Logic
            const contentSections = document.querySelectorAll('.content-section');
            const navLinks = document.querySelectorAll('.nav-link');

            function showSection(targetId) {
                contentSections.forEach(section => {
                    if ('#' + section.id === targetId) {
                        section.classList.add('active');
                    } else {
                        section.classList.remove('active');
                    }
                });

                navLinks.forEach(link => {
                    link.classList.remove('active');
                    if (link.getAttribute('href') === targetId) {
                        link.classList.add('active');
                    }
                });
            }

            // Initial load: show only the first section
            showSection('#studio');

            navLinks.forEach(link => {
                link.addEventListener('click', (event) => {
                    event.preventDefault(); // Prevent default anchor jump
                    const targetId = event.target.getAttribute('href');
                    showSection(targetId);
                    // Close mobile menu after selection
                    if (!mobileMenu.classList.contains('hidden')) {
                         mobileMenu.classList.add('hidden');
                    }
                });
            });

            // Character Data for display and persona
            const characters = {
                subbu: {
                    name: "Kothamangalam Subbu",
                    description: "The No. 2 at Gemini Studios, Subbu was a multi-talented genius—poet, actor, and writer. He was cheerful, incredibly loyal to The Boss, and had a solution for every problem in filmmaking. Despite his perceived sycophancy, he was charitable. He symbolizes the complex blend of creative genius and commercial subservience required to succeed in the studio system. His home was always open to numerous relatives and acquaintances, showcasing his profound generosity, often without conscious thought of the cost. He could suggest dozens of alternatives for a scene, making filmmaking a 'sheer pleasure' for the director.",
                    persona: "You are Kothamangalam Subbu from 'Poets and Pancakes'. You are cheerful, loyal to your boss, incredibly versatile, and always have a practical solution. You speak with a sense of gentle wisdom and optimism, often highlighting the positive aspects of things. You are a 'many-sided genius' and a generous soul. You are known for your knack of coming up with a solution for every cinematic problem."
                },
                officeBoy: {
                    name: "The Office Boy",
                    description: "A man in his forties, despite his title, he was responsible for applying makeup to crowd actors. He harbored grand, unfulfilled dreams of becoming a star, director, screenwriter, or lyricist, and viewed his makeup department role as demeaning, 'fit only for barbers and perverts'. His deep-seated frustration and perceived lack of success led him to blame Subbu, fueling a bitter jealousy. His plight highlights the widespread disillusionment and thwarted dreams that existed just beneath the glamorous surface of the film industry.",
                    persona: "You are the Office Boy from 'Poets and Pancakes'. You are in your forties, deeply frustrated with your makeup job, and bitterly resentful of Kothamangalam Subbu, whom you blame for your stagnated career. You believe your literary talent is being wasted. You often complain and express a strong sense of injustice and unfulfilled ambition."
                },
                lawyer: {
                    name: "The Lawyer (Legal Advisor)",
                    description: "Officially a legal advisor in the Story Department, he was ironically referred to as the 'opposite' by everyone for his cold, logical demeanor in a crowd of dreamers. He was known for his distinct attire (pants and tie, sometimes a coat that looked like 'coat of mail') and his isolated nature. His most notable action was inadvertently ruining the career of a talented, temperamental actress by recording her angry outburst on set, highlighting his detached, calculating nature and the destructive potential of bureaucratic logic in a creative environment.",
                    persona: "You are the Lawyer from 'Poets and Pancakes'. You are a man of cold, detached logic in a studio full of dreamers. You are formal, unemotional, and your actions often have unintended negative consequences. You speak factually, with a hint of bureaucratic dryness and a subtle air of intellectual superiority amidst the creative chaos."
                },
                asokamitran: {
                    name: "Asokamitran (The Narrator)",
                    description: "The author himself, working in a seemingly insignificant job cutting out newspaper clippings, which paradoxically made him one of the most well-informed people in the studio. He is the quiet, ironic observer through whose eyes we see the studio's absurdities and complexities. His keen observational skills, dry wit, and intellectual pursuits outside the studio (like visiting libraries and writing competitions) highlight his role as the detached yet insightful chronicler of Gemini Studios.",
                    persona: "You are Asokamitran, the narrator of 'Poets and Pancakes'. You are a quiet, observant, and often ironic individual who works in a seemingly insignificant role but has a keen insight into the workings of Gemini Studios and its people. You speak with subtle humor and a reflective, analytical tone, often making understated observations and drawing profound conclusions."
                }
            };

            // Character Display Logic
            const characterButtons = document.querySelectorAll('.character-btn');
            const characterNameEl = document.getElementById('character-name');
            const characterDescEl = document.getElementById('character-description');

            characterButtons.forEach(button => {
                button.addEventListener('click', () => {
                    const charKey = button.dataset.character;
                    characterNameEl.textContent = characters[charKey].name;
                    characterDescEl.textContent = characters[charKey].description;

                    characterButtons.forEach(btn => btn.classList.remove('bg-[#D36A5D]', 'text-white'));
                    button.classList.add('bg-[#D36A5D]', 'text-white');
                });
            });

            // Character Radar Chart
            const ctx = document.getElementById('characterChart').getContext('2d');
            const characterChart = new Chart(ctx, {
                type: 'radar',
                data: {
                    labels: ['Creativity', 'Loyalty', 'Ambition', 'Frustration', 'Logic', 'Observation'],
                    datasets: [{
                        label: 'Subbu',
                        data: [9, 9, 7, 2, 5, 6],
                        backgroundColor: 'rgba(211, 106, 93, 0.2)',
                        borderColor: 'rgba(211, 106, 93, 1)',
                        pointBackgroundColor: 'rgba(211, 106, 93, 1)',
                        pointBorderColor: '#fff',
                    }, {
                        label: 'Office Boy',
                        data: [5, 2, 9, 9, 3, 4],
                        backgroundColor: 'rgba(67, 58, 63, 0.2)',
                        borderColor: 'rgba(67, 58, 63, 1)',
                        pointBackgroundColor: 'rgba(67, 58, 63, 1)',
                        pointBorderColor: '#fff',
                    }, {
                        label: 'Lawyer',
                        data: [2, 6, 4, 3, 9, 7],
                        backgroundColor: 'rgba(176, 169, 163, 0.2)',
                        borderColor: 'rgba(176, 169, 163, 1)',
                        pointBackgroundColor: 'rgba(176, 169, 163, 1)',
                        pointBorderColor: '#fff',
                    }, {
                        label: 'Asokamitran',
                        data: [6, 7, 6, 5, 7, 10],
                        backgroundColor: 'rgba(93, 156, 211, 0.2)',
                        borderColor: 'rgba(93, 156, 211, 1)',
                        pointBackgroundColor: 'rgba(93, 156, 211, 1)',
                        pointBorderColor: '#fff',
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            position: 'top',
                        },
                        tooltip: {
                             callbacks: {
                                label: function(context) {
                                    let label = context.dataset.label || '';
                                    if (label) {
                                        label += ': ';
                                    }
                                    if (context.parsed.r !== null) {
                                        label += context.parsed.r;
                                    }
                                    return label;
                                }
                            }
                        }
                    },
                    scales: {
                        r: {
                            angleLines: {
                                display: true
                            },
                            suggestedMin: 0,
                            suggestedMax: 10,
                            pointLabels: {
                                font: {
                                    size: 12,
                                    family: "'Roboto', sans-serif"
                                }
                            }
                        }
                    }
                }
            });

            // Accordion Data
            const themesData = [
                { title: "Humor and Satire 😄", content: "Asokamitran masterfully employs subtle humor and sharp satire to critique the film world without resorting to harsh judgment. This is evident in his vivid descriptions of the makeup room, where actors are transformed into 'hideous crimson hued monsters' under intense lights, highlighting the absurd theatricality of the process. The Office Boy's constant, verbose complaints about his unfulfilled literary ambitions and his bitter resentment towards Subbu are presented with a comedic, yet empathetic, irony. Even the lawyer's seemingly detached, logical approach is subtly satirized in contrast to the dream-driven environment of the studio. The visits of the Moral Re-Armament Army and Stephen Spender also serve as rich sources for satirical commentary, exposing cultural misunderstandings and ideological blind spots. This gentle mocking allows the author to 'poke fun at human flaws' and 'critique the absurdity of bureaucracy' while keeping the narrative engaging and relatable, making the humor a critical tool for analytical depth." },
                { title: "Art vs. Commerce 💰", content: "The narrative of 'Poets and Pancakes' subtly yet powerfully explores the inherent tension between artistic aspirations and the commercial imperatives of filmmaking. The sheer quantity of 'Pancake' makeup, bought in 'truckloads,' symbolizes the commodification of appearance and the industry's obsession with superficial glamour over authentic expression. Kothamangalam Subbu's immense talent, while celebrated, is ultimately channeled to serve the commercial interests of the studio boss, S.S. Vasan. His versatility is not an independent artistic pursuit but a tool for generating marketable film concepts and solving production problems. The studio, despite being a hub of creative activity, operates under a bureaucratic structure that can inadvertently stifle genuine artistic expression. Subbu's genius, though impressive, is not an expression of independent artistic vision but a tool for the boss's commercial success. The rigid hierarchy in makeup and the lawyer's detached, logical presence highlight how the 'dream factory' is also a workplace governed by rules and power dynamics. This suggests a critique of the film industry itself, indicating that the demands of commerce and rigid organizational structures can inadvertently suppress or redirect genuine artistic talent, leading to a tension between creative freedom and industrial routine." },
                { title: "The 'Pancake' Metaphor 🎭", content: "Beyond being a mere brand of makeup, 'Pancake' functions as a central and multifaceted metaphor throughout the chapter. Firstly, it symbolizes the artificiality and superficiality inherent in the film world; it transforms natural appearance into something grotesque yet deemed 'presentable' on screen, emphasizing the illusion at the heart of cinema. Secondly, it highlights the stark contrast between the glamorous 'reel life' presented to the audience and the often arduous, hot, and grim 'real life' behind the scenes for the actors and makeup artists. The excessive use of Pancake underscores the industry's obsession with fabricated appearances and a lack of authenticity. It suggests that individuals, too, might adopt 'pancakes'—metaphorical masks—to conform or succeed within such an environment, obscuring their true selves or ambitions. Thus, 'Pancake' encapsulates themes of illusion, transformation, and the sacrifices made for the sake of show business." },
                { title: "Dreams and Disillusionment 😔", content: "A poignant theme woven through 'Poets and Pancakes' is the exploration of unfulfilled dreams and the harsh realities of the film industry, particularly in a developing post-independence India. The most prominent example is the Office Boy, a man in his forties who harbors grand ambitions of becoming a star, director, screenwriter, or lyricist. His daily task of applying makeup to crowd actors is seen by him as a demeaning waste of his perceived literary talent, leading to deep frustration and resentment towards those he believes have succeeded at his expense (like Subbu). The talented but temperamental actress whose career is inadvertently ruined by the lawyer's detached logic serves as another stark instance of how fragile dreams can be in the cutthroat industry. Asokamitran's own seemingly 'insignificant' job of cutting newspaper clippings, despite his literary inclinations, subtly reflects the pervasive struggle for recognition and the often-unacknowledged contributions of many individuals within the rigid hierarchies of the studio. These characters represent the countless individuals whose artistic aspirations were either crushed or rechanneled by the commercial pressures and rigid structures of the burgeoning film industry, leaving them with a sense of profound disillusionment." }
            ];

            // Accordion Logic
            const accordionContainer = document.getElementById('accordion-container');
            themesData.forEach((item, index) => {
                const accordionItem = document.createElement('div');
                accordionItem.className = 'border border-gray-300 rounded-lg bg-white/50';
                accordionItem.innerHTML = `
                    <button class="accordion-header w-full text-left p-4 flex justify-between items-center">
                        <h3 class="text-lg font-bold">${item.title}</h3>
                        <span class="transform transition-transform duration-300">▼</span>
                    </button>
                    <div class="accordion-content px-4">
                        <p class="py-4 text-gray-700">${item.content}</p>
                        <button class="deep-dive-btn px-4 py-2 my-2 bg-[#A0887D] text-white rounded-lg hover:bg-[#856c63] transition flex items-center justify-center text-sm" data-theme="${item.title.split(' ')[0]}">
                            <span class="deep-dive-text">✨ Deep Dive into Theme ✨</span>
                            <div class="loading-spinner hidden ml-2"></div>
                        </button>
                        <div class="deep-dive-response-container mt-2 p-3 bg-white rounded-md shadow-inner hidden">
                            <h4 class="font-bold text-base mb-1">AI's Deeper Insight:</h4>
                            <p class="deep-dive-response text-gray-700 text-sm"></p>
                        </div>
                    </div>
                `;
                accordionContainer.appendChild(accordionItem);
            });

            const accordionHeaders = document.querySelectorAll('.accordion-header');
            accordionHeaders.forEach(header => {
                header.addEventListener('click', () => {
                    const content = header.nextElementSibling;
                    const arrow = header.querySelector('span');

                    // Close all others
                    accordionHeaders.forEach(otherHeader => {
                        if (otherHeader !== header) {
                            otherHeader.classList.remove('open');
                            otherHeader.nextElementSibling.style.maxHeight = null;
                             otherHeader.nextElementSibling.style.padding = "0px 1rem";
                            otherHeader.querySelector('span').style.transform = 'rotate(0deg)';
                        }
                    });

                    // Toggle current
                    if (content.style.maxHeight) {
                        content.style.maxHeight = null;
                        content.style.padding = "0px 1rem";
                        header.classList.remove('open');
                        arrow.style.transform = 'rotate(0deg)';
                    } else {
                        content.style.maxHeight = content.scrollHeight + "px";
                        content.style.padding = "1rem";
                        header.classList.add('open');
                        arrow.style.transform = 'rotate(180deg)';
                    }
                });
            });


            // Spender Mystery Logic
            const solveBtn = document.getElementById('solve-mystery-btn');
            const mysteryInitial = document.getElementById('mystery-initial');
            const mysterySolved = document.getElementById('mystery-solved');

            solveBtn.addEventListener('click', () => {
                mysteryInitial.classList.add('hidden');
                mysterySolved.classList.remove('hidden');
            });
            
            // Gemini API Integration - Ask the Narrator
            const askNarratorBtn = document.getElementById('ask-narrator-btn');
            const narratorQuestionInput = document.getElementById('narrator-question');
            const narratorResponseContainer = document.getElementById('narrator-response-container');
            const narratorResponseText = document.getElementById('narrator-response');
            const askNarratorText = document.getElementById('ask-narrator-text');
            const askNarratorLoading = document.getElementById('ask-narrator-loading');

            askNarratorBtn.addEventListener('click', async () => {
                const prompt = narratorQuestionInput.value.trim();
                if (!prompt) {
                    narratorResponseText.textContent = "Please enter a question.";
                    narratorResponseContainer.classList.remove('hidden');
                    return;
                }

                askNarratorText.classList.add('hidden');
                askNarratorLoading.classList.remove('hidden');
                narratorResponseContainer.classList.add('hidden');
                narratorResponseText.textContent = ''; // Clear previous response

                try {
                    let chatHistory = [];
                    chatHistory.push({ role: "user", parts: [{ text: `You are an expert literary analyst of Asokamitran's 'Poets and Pancakes'. Answer the following question about the text concisely and informatively: ${prompt}` }] });
                    const payload = { contents: chatHistory };
                    const apiKey = "";
                    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;

                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });

                    const result = await response.json();
                    if (result.candidates && result.candidates.length > 0 &&
                        result.candidates[0].content && result.candidates[0].content.parts &&
                        result.candidates[0].content.parts.length > 0) {
                        const text = result.candidates[0].content.parts[0].text;
                        narratorResponseText.textContent = text;
                    } else {
                        narratorResponseText.textContent = "Sorry, I couldn't generate a response. Please try again.";
                    }
                } catch (error) {
                    console.error("Error calling Gemini API:", error);
                    narratorResponseText.textContent = "An error occurred while fetching the response. Please try again later.";
                } finally {
                    askNarratorText.classList.remove('hidden');
                    askNarratorLoading.classList.add('hidden');
                    narratorResponseContainer.classList.remove('hidden');
                }
            });

            // Gemini API Integration - Deep Dive into Theme
            const deepDiveButtons = document.querySelectorAll('.deep-dive-btn');

            deepDiveButtons.forEach(button => {
                button.addEventListener('click', async () => {
                    const themeTitle = button.dataset.theme;
                    const deepDiveTextEl = button.querySelector('.deep-dive-text');
                    const loadingSpinnerEl = button.querySelector('.loading-spinner');
                    const responseContainer = button.nextElementSibling;
                    const responseTextEl = responseContainer.querySelector('.deep-dive-response');

                    deepDiveTextEl.classList.add('hidden');
                    loadingSpinnerEl.classList.remove('hidden');
                    responseContainer.classList.add('hidden');
                    responseTextEl.textContent = ''; // Clear previous response

                    try {
                        let chatHistory = [];
                        chatHistory.push({ role: "user", parts: [{ text: `Provide a deeper explanation and perhaps an additional example for the literary theme of '${themeTitle}' as it appears in Asokamitran's 'Poets and Pancakes'. Keep the response concise, within 150 words.` }] });
                        const payload = { contents: chatHistory };
                        const apiKey = "";
                        const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;

                        const response = await fetch(apiUrl, {
                            method: 'POST',
                            headers: { 'Content-Type': 'application/json' },
                            body: JSON.stringify(payload)
                        });

                        const result = await response.json();
                        if (result.candidates && result.candidates.length > 0 &&
                            result.candidates[0].content && result.candidates[0].content.parts &&
                            result.candidates[0].content.parts.length > 0) {
                            const text = result.candidates[0].content.parts[0].text;
                            responseTextEl.textContent = text;
                        } else {
                            responseTextEl.textContent = "Sorry, couldn't get a deeper insight. Please try again.";
                        }
                    } catch (error) {
                        console.error("Error calling Gemini API for deep dive:", error);
                        responseTextEl.textContent = "An error occurred. Please try again.";
                    } finally {
                        deepDiveTextEl.classList.remove('hidden');
                        loadingSpinnerEl.classList.add('hidden');
                        responseContainer.classList.remove('hidden');
                    }
                });
            });

            // Gemini API Integration - Character Dialogue Simulator
            const characterSelect = document.getElementById('character-select');
            const userDialogueInput = document.getElementById('user-dialogue-input');
            const sendDialogueBtn = document.getElementById('send-dialogue-btn');
            const sendDialogueText = document.getElementById('send-dialogue-text');
            const sendDialogueLoading = document.getElementById('send-dialogue-loading');
            const characterDialogueResponseContainer = document.getElementById('character-dialogue-response-container');
            const characterDialogueResponse = document.getElementById('character-dialogue-response');
            const dialogueResponseHeading = document.getElementById('dialogue-response-heading');

            sendDialogueBtn.addEventListener('click', async () => {
                const selectedCharacterKey = characterSelect.value;
                const userMessage = userDialogueInput.value.trim();

                if (!userMessage) {
                    characterDialogueResponse.textContent = "Please type a message to the character.";
                    characterDialogueResponseContainer.classList.remove('hidden');
                    return;
                }

                sendDialogueText.classList.add('hidden');
                sendDialogueLoading.classList.remove('hidden');
                characterDialogueResponseContainer.classList.add('hidden');
                characterDialogueResponse.textContent = ''; // Clear previous response

                try {
                    const characterPersona = characters[selectedCharacterKey].persona;
                    const characterName = characters[selectedCharacterKey].name;

                    let chatHistory = [];
                    chatHistory.push({ role: "user", parts: [{ text: `${characterPersona}\n\nUser: ${userMessage}\n\n${characterName}: ` }] });
                    const payload = { contents: chatHistory };
                    const apiKey = "";
                    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;

                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });

                    const result = await response.json();
                    if (result.candidates && result.candidates.length > 0 &&
                        result.candidates[0].content && result.candidates[0].content.parts &&
                        result.candidates[0].content.parts.length > 0) {
                        const text = result.candidates[0].content.parts[0].text;
                        dialogueResponseHeading.textContent = `${characterName}'s Response:`;
                        characterDialogueResponse.textContent = text;
                    } else {
                        dialogueResponseHeading.textContent = "Error:";
                        characterDialogueResponse.textContent = `Sorry, ${characterName} is unable to respond right now. Please try again.`;
                    }
                } catch (error) {
                    console.error("Error calling Gemini API for character dialogue:", error);
                    dialogueResponseHeading.textContent = "Error:";
                    characterDialogueResponse.textContent = "An error occurred while simulating the dialogue. Please try again later.";
                } finally {
                    sendDialogueText.classList.remove('hidden');
                    sendDialogueLoading.classList.add('hidden');
                    characterDialogueResponseContainer.classList.remove('hidden');
                }
            });
        });
    </script>
</body>
</html>

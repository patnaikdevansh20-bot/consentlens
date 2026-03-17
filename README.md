<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ConsentLens – Understand Before You Agree</title>
    <!-- Modern, clean font -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        /* --- CSS VARIABLES FOR THEMING --- */
        :root {
            --primary: #2563eb;       /* Bright modern blue */
            --primary-hover: #1d4ed8;
            --primary-light: #dbeafe;
            --bg-color: #f8fafc;      /* Very light cool gray */
            --white: #ffffff;
            --text-dark: #0f172a;
            --text-muted: #64748b;
            
            /* Status Colors */
            --danger: #ef4444;        /* Red for High Risk */
            --danger-bg: #fef2f2;
            --warning: #f59e0b;       /* Yellow for Medium Risk */
            --warning-bg: #fffbeb;
            --success: #10b981;       /* Green for Low Risk */
            --success-bg: #ecfdf5;
            
            --border-radius-lg: 16px;
            --border-radius-md: 12px;
            
            /* Modern soft shadows */
            --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
            --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
            --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
            --shadow-hover: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
        }

        /* --- GLOBAL RESET & TYPOGRAPHY --- */
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Inter', sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-dark);
            line-height: 1.6;
            padding: 3rem 1.5rem;
            -webkit-font-smoothing: antialiased;
        }

        .container {
            max-width: 900px;
            margin: 0 auto;
        }

        /* --- HEADER --- */
        header {
            text-align: center;
            margin-bottom: 3rem;
        }

        h1 {
            color: var(--text-dark);
            font-size: 2.5rem;
            font-weight: 800;
            margin-bottom: 0.75rem;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 12px;
            letter-spacing: -0.025em;
        }

        h1 svg {
            color: var(--primary);
        }

        header p {
            color: var(--text-muted);
            font-size: 1.15rem;
            max-width: 600px;
            margin: 0 auto;
        }

        /* --- INPUT CARD --- */
        .input-card {
            background-color: var(--white);
            padding: 2.5rem;
            border-radius: var(--border-radius-lg);
            box-shadow: var(--shadow-md);
            margin-bottom: 2rem;
            border: 1px solid #e2e8f0;
        }

        label {
            display: block;
            font-weight: 600;
            margin-bottom: 0.75rem;
            color: var(--text-dark);
            font-size: 1.05rem;
        }

        textarea {
            width: 100%;
            height: 220px;
            padding: 1.25rem;
            border: 2px solid #e2e8f0;
            border-radius: var(--border-radius-md);
            font-size: 1rem;
            resize: vertical;
            margin-bottom: 1.5rem;
            transition: all 0.3s ease;
            background-color: #f8fafc;
            color: var(--text-dark);
            line-height: 1.5;
        }

        textarea:focus {
            outline: none;
            border-color: var(--primary);
            background-color: var(--white);
            box-shadow: 0 0 0 4px var(--primary-light);
        }

        .btn-primary {
            background-color: var(--primary);
            color: var(--white);
            border: none;
            padding: 1.1rem 2rem;
            font-size: 1.1rem;
            font-weight: 600;
            border-radius: var(--border-radius-md);
            cursor: pointer;
            width: 100%;
            transition: all 0.2s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            box-shadow: var(--shadow-sm);
        }

        .btn-primary:hover { 
            background-color: var(--primary-hover); 
            box-shadow: var(--shadow-md);
            transform: translateY(-1px);
        }
        .btn-primary:active { transform: translateY(1px); }
        .btn-primary:disabled { 
            background-color: #94a3b8; 
            cursor: not-allowed; 
            transform: none;
            box-shadow: none;
        }

        /* --- LOADING ANIMATION --- */
        .loader-container {
            display: none;
            text-align: center;
            padding: 3rem 2rem;
            background: var(--white);
            border-radius: var(--border-radius-lg);
            box-shadow: var(--shadow-md);
            margin-bottom: 2rem;
            border: 1px solid #e2e8f0;
        }

        .radar-spinner {
            position: relative;
            width: 60px;
            height: 60px;
            margin: 0 auto 1.5rem auto;
        }

        .radar-spinner::before, .radar-spinner::after {
            content: "";
            position: absolute;
            top: 0; left: 0; right: 0; bottom: 0;
            border-radius: 50%;
            border: 3px solid var(--primary);
            opacity: 0;
            animation: pulse 2s linear infinite;
        }
        
        .radar-spinner::after {
            animation-delay: 1s;
        }

        @keyframes pulse {
            0% { transform: scale(0.5); opacity: 1; }
            100% { transform: scale(1.5); opacity: 0; }
        }

        .loader-text {
            color: var(--primary);
            font-weight: 600;
            font-size: 1.1rem;
            animation: breathe 2s ease-in-out infinite;
        }

        @keyframes breathe {
            0%, 100% { opacity: 0.6; }
            50% { opacity: 1; }
        }

        /* --- RESULTS AREA --- */
        #results-section { display: none; }

        /* Score Card & Meter */
        .score-card {
            text-align: center;
            padding: 3rem 2rem;
            background-color: var(--white);
            border-radius: var(--border-radius-lg);
            box-shadow: var(--shadow-lg);
            margin-bottom: 2rem;
            border: 1px solid #e2e8f0;
            position: relative;
            overflow: hidden;
            transition: all 0.3s ease;
        }

        .score-card::before {
            content: '';
            position: absolute;
            top: 0; left: 0; right: 0;
            height: 6px;
            background-color: var(--text-muted); /* Default, updated via JS */
            id: score-top-bar;
            transition: background-color 0.5s ease;
        }

        .score-value-container {
            display: flex;
            align-items: baseline;
            justify-content: center;
            gap: 5px;
            margin: 1.5rem 0 0.5rem 0;
        }

        .score-value {
            font-size: 5rem;
            font-weight: 800;
            line-height: 1;
            transition: color 0.5s ease;
        }

        .score-max {
            font-size: 2rem;
            color: var(--text-muted);
            font-weight: 600;
        }

        /* The Animated Risk Meter */
        .risk-meter-wrapper {
            max-width: 400px;
            margin: 1.5rem auto;
        }

        .risk-meter-bg {
            height: 12px;
            background-color: #e2e8f0;
            border-radius: 999px;
            overflow: hidden;
            position: relative;
        }

        .risk-meter-fill {
            height: 100%;
            width: 0%; /* Animated via JS */
            background-color: var(--text-muted); /* Default */
            border-radius: 999px;
            transition: width 1.5s cubic-bezier(0.22, 1, 0.36, 1), background-color 0.5s ease;
        }

        .risk-labels {
            display: flex;
            justify-content: space-between;
            margin-top: 0.5rem;
            font-size: 0.85rem;
            color: var(--text-muted);
            font-weight: 500;
        }

        #score-summary {
            font-size: 1.2rem;
            font-weight: 600;
            margin-top: 1rem;
            padding: 0.75rem 1.5rem;
            border-radius: 999px;
            display: inline-block;
            transition: all 0.5s ease;
        }

        /* Details Grid Cards */
        .result-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 1.5rem;
        }

        @media (min-width: 768px) {
            .result-grid { grid-template-columns: repeat(3, 1fr); }
        }

        .result-box {
            background-color: var(--white);
            padding: 2rem 1.5rem;
            border-radius: var(--border-radius-lg);
            box-shadow: var(--shadow-md);
            border: 1px solid #e2e8f0;
            transition: transform 0.2s ease, box-shadow 0.2s ease;
            height: 100%;
        }

        .result-box:hover {
            transform: translateY(-5px);
            box-shadow: var(--shadow-hover);
        }

        .result-header {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-bottom: 1.25rem;
            padding-bottom: 1rem;
            border-bottom: 2px solid #f1f5f9;
        }

        .result-header h3 {
            color: var(--text-dark);
            font-size: 1.25rem;
            font-weight: 700;
        }

        /* Dynamic Icons per section */
        .icon-risk { color: var(--danger); }
        .icon-privacy { color: var(--warning); }
        .icon-consent { color: var(--primary); }

        .result-box ul { list-style-type: none; }

        .result-box ul li {
            position: relative;
            padding-left: 1.75rem;
            margin-bottom: 1rem;
            font-size: 0.95rem;
            color: var(--text-muted);
            line-height: 1.5;
        }

        /* Custom Bullet Points */
        .result-box ul li::before {
            content: "";
            position: absolute;
            left: 0;
            top: 6px;
            width: 8px;
            height: 8px;
            border-radius: 50%;
            background-color: var(--primary);
            opacity: 0.7;
        }
        
        .error-message {
            background-color: var(--danger-bg);
            color: var(--danger);
            padding: 1rem;
            border-radius: var(--border-radius-md);
            margin-top: 1.5rem;
            text-align: center;
            display: none;
            font-weight: 500;
            border: 1px solid #fca5a5;
        }

        .api-note {
            font-size: 0.85rem;
            color: var(--text-muted);
            text-align: center;
            margin-top: 1rem;
        }

    </style>
</head>
<body>

    <div class="container">
        <!-- Application Header -->
        <header>
            <h1>
                <svg width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"></path>
                    <circle cx="12" cy="11" r="3"></circle>
                </svg>
                ConsentLens
            </h1>
            <p>AI-Powered Legal Analysis. Understand exactly what you're agreeing to before you click accept.</p>
        </header>

        <main>
            <!-- Input Section -->
            <section class="input-card">
                <label for="legal-text">Paste Terms & Conditions or Privacy Policy:</label>
                <textarea id="legal-text" placeholder="e.g. By using our services, you agree to grant us a non-exclusive, worldwide, royalty-free license to use, reproduce, and distribute your content..."></textarea>
                
                <button id="analyze-btn" class="btn-primary" onclick="startAnalysis()">
                    <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                        <circle cx="11" cy="11" r="8"></circle>
                        <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
                    </svg>
                    Analyze Document with AI
                </button>
                
                <p class="api-note">Powered by Gemini AI. Extracts key risks, privacy issues, and consent concerns.</p>
                <div id="error-box" class="error-message"></div>
            </section>

            <!-- Modern Loading State -->
            <section id="loading-section" class="loader-container">
                <div class="radar-spinner"></div>
                <p class="loader-text">AI is reading and analyzing the legal text...</p>
            </section>

            <!-- Results Section -->
            <section id="results-section">
                <!-- Score Card & Meter -->
                <div class="score-card" id="score-card-container">
                    <h2>Overall Risk Score</h2>
                    
                    <div class="score-value-container">
                        <div id="risk-score" class="score-value">-</div>
                        <div class="score-max">/ 10</div>
                    </div>
                    
                    <!-- New Visual Risk Meter -->
                    <div class="risk-meter-wrapper">
                        <div class="risk-meter-bg">
                            <div id="risk-meter-fill" class="risk-meter-fill"></div>
                        </div>
                        <div class="risk-labels">
                            <span>Safe (1)</span>
                            <span>Caution (5)</span>
                            <span>Risky (10)</span>
                        </div>
                    </div>

                    <p id="score-summary"></p>
                </div>

                <!-- Detailed Breakdown Grid -->
                <div class="result-grid">
                    <!-- Key Risks Card -->
                    <div class="result-box">
                        <div class="result-header">
                            <svg class="icon-risk" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                                <path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"></path>
                                <line x1="12" y1="9" x2="12" y2="13"></line>
                                <line x1="12" y1="17" x2="12.01" y2="17"></line>
                            </svg>
                            <h3>Key Legal Risks</h3>
                        </div>
                        <ul id="list-key-risks"></ul>
                    </div>
                    
                    <!-- Data Privacy Card -->
                    <div class="result-box">
                        <div class="result-header">
                            <svg class="icon-privacy" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                                <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path>
                                <circle cx="12" cy="12" r="3"></circle>
                            </svg>
                            <h3>Data Privacy</h3>
                        </div>
                        <ul id="list-data-privacy"></ul>
                    </div>

                    <!-- Consent Concerns Card -->
                    <div class="result-box">
                        <div class="result-header">
                            <svg class="icon-consent" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                                <rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect>
                                <path d="M9 12l2 2 4-4"></path>
                            </svg>
                            <h3>Consent Issues</h3>
                        </div>
                        <ul id="list-consent"></ul>
                    </div>
                </div>
            </section>
        </main>
    </div>

    <script>
        // ==========================================
        // API CONFIGURATION
        // ==========================================
        // The execution environment provides the key at runtime automatically.
        // To use locally, place your Gemini API key between the quotes.
        const apiKey = "AIzaSyAMa77rpfATYkDVfh54M1rFL4b1Qo2C_ek"; 

        // DOM Elements
        const analyzeBtn = document.getElementById('analyze-btn');
        const legalTextInput = document.getElementById('legal-text');
        const loadingSection = document.getElementById('loading-section');
        const resultsSection = document.getElementById('results-section');
        const errorBox = document.getElementById('error-box');

        // ==========================================
        // MAIN ANALYSIS FUNCTION
        // ==========================================
        async function startAnalysis() {
            const text = legalTextInput.value.trim();
            
            if (!text || text.length < 30) {
                showError("Please paste a sufficient amount of legal text to analyze.");
                return;
            }

            // Reset UI State for new analysis
            errorBox.style.display = 'none';
            resultsSection.style.display = 'none';
            loadingSection.style.display = 'block';
            analyzeBtn.disabled = true;
            analyzeBtn.innerHTML = "Analyzing..."; // Change button text

            // Reset Meter width so it animates from 0 again
            document.getElementById('risk-meter-fill').style.width = '0%';

            try {
                // Call Gemini API
                const analysisData = await fetchAnalysisFromAI(text);
                
                // Render UI updates
                renderResults(analysisData);

            } catch (error) {
                console.error("Analysis Error:", error);
                showError("API Error: Unable to complete analysis. If running locally, please ensure your API key is correctly added.");
            } finally {
                loadingSection.style.display = 'none';
                analyzeBtn.disabled = false;
                // Restore button content
                analyzeBtn.innerHTML = `
                    <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                        <circle cx="11" cy="11" r="8"></circle>
                        <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
                    </svg>
                    Analyze Document with AI
                `;
            }
        }

        // ==========================================
        // AI INTEGRATION LOGIC (GEMINI API)
        // ==========================================
        async function fetchAnalysisFromAI(text) {
            const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
            
            const systemPrompt = `
You are an expert legal-tech AI assistant.

Analyze the provided Terms & Conditions or Privacy Policy.

Identify:
1. Key legal risks (unfair terms, liability clauses, arbitration issues)
2. Data privacy concerns (data sharing, tracking, third-party access)
3. Consent issues (forced consent, dark patterns, lack of clarity)

IMPORTANT:
- Also evaluate from an Indian legal perspective (consumer protection, data protection principles).
- Flag anything that may make consent legally questionable.

OUTPUT:
- Keep points simple and understandable for a normal user.
- Be concise but insightful.

Return:
- keyRisks (2–4 points)
- dataPrivacyIssues (2–4 points)
- consentConcerns (2–4 points)
- riskScore (1–10)
- summary (1 line, strong conclusion)
`;

            const payload = {
                contents: [{ parts: [{ text: text }] }],
                systemInstruction: { parts: [{ text: systemPrompt }] },
                generationConfig: {
                    responseMimeType: "application/json",
                    responseSchema: {
                        type: "OBJECT",
                        properties: {
                            keyRisks: { type: "ARRAY", items: { type: "STRING" }, description: "List of 2-4 major legal risks found." },
                            dataPrivacyIssues: { type: "ARRAY", items: { type: "STRING" }, description: "List of 2-4 data privacy concerns." },
                            consentConcerns: { type: "ARRAY", items: { type: "STRING" }, description: "List of 2-4 issues with how consent is forced or gathered." },
                            riskScore: { type: "INTEGER", description: "A score from 1 to 10 based on user risk." },
                            summary: { type: "STRING", description: "A concise 1-sentence summary of the overall risk." }
                        },
                        required: ["keyRisks", "dataPrivacyIssues", "consentConcerns", "riskScore", "summary"]
                    }
                }
            };

            let retries = 5;
            let delayMs = 1000;
            
            while (retries > 0) {
                try {
                    const response = await fetch(url, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });

                    if (!response.ok) throw new Error(`HTTP ${response.status}`);
                    
                    const result = await response.json();
                    const jsonText = result.candidates[0].content.parts[0].text;
                    return JSON.parse(jsonText);

                } catch (err) {
                    retries--;
                    if (retries === 0) throw err;
                    await new Promise(resolve => setTimeout(resolve, delayMs));
                    delayMs *= 2; 
                }
            }
        }

        // ==========================================
        // UI RENDERING UTILITIES
        // ==========================================
        function renderResults(data) {
            // 1. Setup Elements
            const scoreElement = document.getElementById('risk-score');
            const meterFill = document.getElementById('risk-meter-fill');
            const scoreContainer = document.getElementById('score-card-container');
            const summaryElement = document.getElementById('score-summary');
            
            // 2. Determine Color Palette based on score
            let themeColor, themeBg;
            if (data.riskScore <= 3) {
                themeColor = 'var(--success)';
                themeBg = 'var(--success-bg)';
            } else if (data.riskScore <= 7) {
                themeColor = 'var(--warning)';
                themeBg = 'var(--warning-bg)';
            } else {
                themeColor = 'var(--danger)';
                themeBg = 'var(--danger-bg)';
            }

            // 3. Apply Texts and Colors
            scoreElement.textContent = data.riskScore;
            scoreElement.style.color = themeColor;
            
            // Apply top border color via custom property logic or inline style
            scoreContainer.style.setProperty('--text-muted', themeColor); // Hacks the pseudo-element before color
            
            summaryElement.textContent = data.summary;
            summaryElement.style.backgroundColor = themeBg;
            summaryElement.style.color = themeColor;

            // 4. Populate Lists
            const populateList = (elementId, items) => {
                const ul = document.getElementById(elementId);
                ul.innerHTML = ''; 
                if (!items || items.length === 0) {
                    ul.innerHTML = '<li>No significant issues found in this category.</li>';
                    return;
                }
                items.forEach(item => {
                    const li = document.createElement('li');
                    li.textContent = item;
                    ul.appendChild(li);
                });
            };

            populateList('list-key-risks', data.keyRisks);
            populateList('list-data-privacy', data.dataPrivacyIssues);
            populateList('list-consent', data.consentConcerns);

            // 5. Show Results and Animate Meter
            resultsSection.style.display = 'block';
            
            // Calculate percentage for the meter (ensure it doesn't exceed 100%)
            const scorePercentage = Math.min((data.riskScore / 10) * 100, 100);
            
            // Small delay to allow CSS transition to trigger properly after display:block
            setTimeout(() => {
                meterFill.style.width = `${scorePercentage}%`;
                meterFill.style.backgroundColor = themeColor;
                
                // Scroll to view
                resultsSection.scrollIntoView({ behavior: 'smooth', block: 'start' });
            }, 50);
        }

        function showError(message) {
            errorBox.textContent = message;
            errorBox.style.display = 'block';
        }
    </script>
</body>
</html>

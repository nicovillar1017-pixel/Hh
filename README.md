# H&H
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>The Sunny Side | Good Vibes Blog</title>
    
    <!-- React & ReactDOM -->
    <script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    
    <!-- Babel for JSX -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@300;400;500;600&family=Nunito:wght@400;600;700&display=swap" rel="stylesheet">

    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>

    <style>
        body {
            font-family: 'Nunito', sans-serif;
            background-color: #FFFDF5; /* Soft cream background */
        }
        h1, h2, h3, h4, h5, h6 {
            font-family: 'Fredoka', sans-serif;
        }
        .watercolor-bg {
            background-image: url('https://images.unsplash.com/photo-1579783902614-a3fb3927b6a5?ixlib=rb-4.0.3&auto=format&fit=crop&w=2000&q=80');
            background-size: cover;
            background-position: center;
        }
        .card-hover {
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }
        .card-hover:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.1);
        }
        .blob {
            position: absolute;
            filter: blur(40px);
            z-index: 0;
            opacity: 0.5;
        }
        /* Loading Spinner */
        .spinner {
            border: 3px solid rgba(255,255,255,0.3);
            border-radius: 50%;
            border-top: 3px solid #ffffff;
            width: 20px;
            height: 20px;
            -webkit-animation: spin 1s linear infinite; /* Safari */
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect } = React;
        const apiKey = ""; // System will provide the key

        // Gemini API Helper
        async function callGemini(prompt) {
            try {
                const response = await fetch(
                    `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`,
                    {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json',
                        },
                        body: JSON.stringify({
                            contents: [{
                                parts: [{ text: prompt }]
                            }]
                        })
                    }
                );
                
                if (!response.ok) {
                    throw new Error(`API call failed: ${response.statusText}`);
                }

                const data = await response.json();
                return data.candidates?.[0]?.content?.parts?.[0]?.text || "Sending good vibes your way!";
            } catch (error) {
                console.error("Gemini API Error:", error);
                return null; // Return null to trigger fallback
            }
        }

        // Simple icon components
        const Icon = ({ name, size = 24, className }) => {
            const icons = {
                sun: <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><circle cx="12" cy="12" r="4"/><path d="M12 2v2"/><path d="M12 20v2"/><path d="m4.93 4.93 1.41 1.41"/><path d="m17.66 17.66 1.41 1.41"/><path d="M2 12h2"/><path d="M20 12h2"/><path d="m6.34 17.66-1.41 1.41"/><path d="m19.07 4.93-1.41 1.41"/></svg>,
                heart: <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M19 14c1.49-1.46 3-3.21 3-5.5A5.5 5.5 0 0 0 16.5 3c-1.76 0-3 .5 4.5 2-1.5-1.5-2.74-2-4.5-2A5.5 5.5 0 0 0 2 8.5c0 2.3 1.5 4.05 3 5.5l7 7Z"/></svg>,
                coffee: <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M17 8h1a4 4 0 1 1 0 8h-1"/><path d="M3 8h14v9a4 4 0 0 1-4 4H7a4 4 0 0 1-4-4Z"/><line x1="6" x2="6" y1="2" y2="4"/><line x1="10" x2="10" y1="2" y2="4"/><line x1="14" x2="14" y1="2" y2="4"/></svg>,
                sparkles: <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="m12 3-1.912 5.813a2 2 0 0 1-1.275 1.275L3 12l5.813 1.912a2 2 0 0 1 1.275 1.275L12 21l1.912-5.813a2 2 0 0 1 1.275-1.275L21 12l-5.813-1.912a2 2 0 0 1-1.275-1.275Z"/></svg>,
                arrowRight: <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>,
                banana: <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M4 13c3.5-2 8-2 10 2a5.5 5.5 0 0 1 8 5" /><path d="M5.15 17a9 9 0 1 0 17.77-4.4" /></svg>,
                refresh: <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M21 12a9 9 0 1 1-9-9c2.52 0 4.93 1 6.74 2.74L21 8"/><path d="M21 3v5h-5"/></svg>,
                edit: <svg xmlns="http://www.w3.org/2000/svg" width={size} height={size} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/><path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/></svg>
            };
            return icons[name] || null;
        };

        const posts = [
            {
                id: 1,
                title: "Getting Started with Nano Banana Pro 🍌",
                category: "Creative Tools",
                excerpt: "Discover how to turn your selfies into watercolor masterpieces with our latest tutorial comic.",
                color: "bg-yellow-100",
                textColor: "text-yellow-800",
                icon: "banana",
                date: "Oct 24, 2023"
            },
            {
                id: 2,
                title: "Finding Joy in the Little Things",
                category: "Mindfulness",
                excerpt: "Why your morning coffee tastes better when you slow down and appreciate the steam.",
                color: "bg-blue-100",
                textColor: "text-blue-800",
                icon: "coffee",
                date: "Oct 22, 2023"
            },
            {
                id: 3,
                title: "The Art of Soft Living",
                category: "Lifestyle",
                excerpt: "Embracing pastels, comfort, and kindness in a fast-paced world.",
                color: "bg-pink-100",
                textColor: "text-pink-800",
                icon: "heart",
                date: "Oct 20, 2023"
            }
        ];

        const fallbackQuotes = [
            "You are doing better than you think.",
            "Radiate positivity like sunshine.",
            "Creativity is intelligence having fun.",
            "Today is a perfect day to start.",
            "Breathe in calm, breathe out stress.",
            "Your vibe attracts your tribe."
        ];

        function App() {
            const [currentQuote, setCurrentQuote] = useState(fallbackQuotes[0]);
            const [isGeneratingQuote, setIsGeneratingQuote] = useState(false);
            const [email, setEmail] = useState("");
            const [subscribed, setSubscribed] = useState(false);

            // Reframing Tool State
            const [thought, setThought] = useState("");
            const [reframedThought, setReframedThought] = useState("");
            const [isReframing, setIsReframing] = useState(false);

            const generateAIQuote = async () => {
                setIsGeneratingQuote(true);
                const prompt = "Write a single, short, whimsical, and uplifting sentence that acts as a 'daily vibe'. It should be unique, comforting, and less than 15 words. Avoid clichés.";
                const result = await callGemini(prompt);
                
                if (result) {
                    setCurrentQuote(result.trim());
                } else {
                    // Fallback if API fails
                    const randomQuote = fallbackQuotes[Math.floor(Math.random() * fallbackQuotes.length)];
                    setCurrentQuote(randomQuote);
                }
                setIsGeneratingQuote(false);
            };

            const handleReframe = async () => {
                if (!thought.trim()) return;
                setIsReframing(true);
                setReframedThought("");
                
                const prompt = `Read this user's thought: "${thought}". Rewrite this thought to be more positive, compassionate, and constructive. Keep it brief (under 2 sentences) and friendly. Act like a supportive friend.`;
                const result = await callGemini(prompt);
                
                if (result) {
                    setReframedThought(result.trim());
                } else {
                    setReframedThought("It seems I couldn't connect to the positivity cloud right now, but remember: be gentle with yourself.");
                }
                setIsReframing(false);
            };

            const handleSubscribe = (e) => {
                e.preventDefault();
                if(email) setSubscribed(true);
            };

            return (
                <div className="min-h-screen relative overflow-hidden">
                    {/* Background Blobs */}
                    <div className="blob bg-yellow-200 w-96 h-96 rounded-full -top-20 -left-20 mix-blend-multiply filter blur-3xl opacity-30 animate-blob"></div>
                    <div className="blob bg-pink-200 w-96 h-96 rounded-full top-40 -right-20 mix-blend-multiply filter blur-3xl opacity-30 animate-blob animation-delay-2000"></div>
                    <div className="blob bg-blue-200 w-96 h-96 rounded-full -bottom-32 left-20 mix-blend-multiply filter blur-3xl opacity-30 animate-blob animation-delay-4000"></div>

                    {/* Navigation */}
                    <nav className="relative z-10 px-6 py-6 flex justify-between items-center max-w-6xl mx-auto">
                        <div className="flex items-center gap-2">
                            <div className="bg-yellow-400 p-2 rounded-full text-white shadow-lg">
                                <Icon name="sun" size={24} />
                            </div>
                            <span className="text-2xl font-bold text-gray-800 tracking-tight">The Sunny Side</span>
                        </div>
                        <div className="hidden md:flex gap-8 font-semibold text-gray-600">
                            <a href="#" className="hover:text-yellow-500 transition-colors">Home</a>
                            <a href="#" className="hover:text-yellow-500 transition-colors">Journal</a>
                            <a href="#" className="hover:text-yellow-500 transition-colors">About</a>
                            <a href="#" className="hover:text-yellow-500 transition-colors">Contact</a>
                        </div>
                        <button className="bg-gray-800 text-white px-5 py-2 rounded-full font-semibold hover:bg-gray-700 transition-colors shadow-md transform hover:scale-105 active:scale-95 duration-200">
                            Subscribe
                        </button>
                    </nav>

                    {/* Hero Section */}
                    <header className="relative z-10 max-w-4xl mx-auto text-center mt-12 mb-20 px-4">
                        <span className="inline-block px-4 py-1.5 rounded-full bg-orange-100 text-orange-600 font-bold text-sm mb-6 tracking-wide uppercase">
                            Welcome to your happy place
                        </span>
                        <h1 className="text-5xl md:text-7xl font-bold text-gray-800 mb-6 leading-tight">
                            Create, Inspire, <br/> & <span className="text-transparent bg-clip-text bg-gradient-to-r from-yellow-400 to-pink-500">Vibe Check.</span>
                        </h1>
                        <p className="text-xl text-gray-600 mb-10 max-w-2xl mx-auto leading-relaxed">
                            A digital sanctuary for creativity, mindfulness, and soft aesthetics. Grab a tea, explore our tutorials, and let the good vibes roll.
                        </p>
                        
                        {/* AI Vibe Generator */}
                        <div className="bg-white/80 backdrop-blur-sm p-8 rounded-3xl shadow-xl max-w-xl mx-auto border border-white/50 relative overflow-hidden group">
                            <div className="absolute top-0 left-0 w-full h-1 bg-gradient-to-r from-yellow-300 via-pink-300 to-blue-300"></div>
                            <div className="flex justify-between items-center mb-4">
                                <h3 className="text-gray-400 text-xs font-bold uppercase tracking-widest">✨ Powered by Gemini</h3>
                                {isGeneratingQuote && <span className="text-xs text-yellow-500 font-bold animate-pulse">Dreaming up a vibe...</span>}
                            </div>
                            
                            <p className="text-2xl font-bold text-gray-800 mb-6 min-h-[4rem] flex items-center justify-center">
                                "{currentQuote}"
                            </p>
                            
                            <button 
                                onClick={generateAIQuote}
                                disabled={isGeneratingQuote}
                                className="inline-flex items-center gap-2 bg-gradient-to-r from-yellow-400 to-orange-400 text-white px-6 py-3 rounded-xl font-bold shadow-lg hover:shadow-orange-200/50 hover:-translate-y-1 transition-all duration-300 disabled:opacity-70 disabled:cursor-not-allowed"
                            >
                                {isGeneratingQuote ? <div className="spinner"></div> : <Icon name="sparkles" size={20} />}
                                {isGeneratingQuote ? "Generating..." : "Generate AI Vibe"}
                            </button>
                        </div>
                    </header>

                    {/* New AI Feature: The Perspective Shift */}
                    <section className="relative z-10 max-w-4xl mx-auto px-6 mb-24">
                        <div className="bg-white rounded-[2rem] shadow-xl p-8 md:p-12 border border-blue-100 relative overflow-hidden">
                            <div className="absolute top-0 right-0 w-40 h-40 bg-blue-100 rounded-full mix-blend-multiply filter blur-2xl opacity-50 -mr-10 -mt-10"></div>
                            
                            <div className="relative z-10 grid md:grid-cols-2 gap-12 items-center">
                                <div>
                                    <div className="inline-flex items-center gap-2 text-blue-500 font-bold text-sm mb-2">
                                        <Icon name="edit" size={16} />
                                        <span>Turn it Around</span>
                                    </div>
                                    <h2 className="text-3xl font-bold text-gray-800 mb-4">The Perspective Shift</h2>
                                    <p className="text-gray-600 mb-6">
                                        Having a tough moment? Type out what's bothering you, and let our AI help you find a gentler, more constructive way to look at it.
                                    </p>
                                    
                                    <div className="space-y-4">
                                        <textarea
                                            value={thought}
                                            onChange={(e) => setThought(e.target.value)}
                                            placeholder="I'm feeling stressed because..."
                                            className="w-full p-4 rounded-xl bg-gray-50 border border-gray-200 focus:border-blue-400 focus:ring-2 focus:ring-blue-100 outline-none resize-none h-32 transition-all"
                                        />
                                        <button 
                                            onClick={handleReframe}
                                            disabled={isReframing || !thought}
                                            className="w-full bg-blue-500 text-white font-bold py-3 rounded-xl hover:bg-blue-600 transition-colors shadow-lg shadow-blue-500/30 flex items-center justify-center gap-2 disabled:opacity-50 disabled:cursor-not-allowed"
                                        >
                                            {isReframing ? <div className="spinner"></div> : "✨ Reframe with Positivity"}
                                        </button>
                                    </div>
                                </div>

                                <div className="bg-blue-50/50 rounded-2xl p-6 h-full flex flex-col min-h-[300px] border border-blue-100">
                                    <h3 className="text-blue-800 font-bold mb-4 flex items-center gap-2">
                                        <Icon name="sun" size={18} />
                                        New Perspective
                                    </h3>
                                    <div className="flex-1 flex items-center justify-center text-center">
                                        {reframedThought ? (
                                            <p className="text-lg text-gray-700 font-medium leading-relaxed animate-in fade-in duration-700">
                                                "{reframedThought}"
                                            </p>
                                        ) : (
                                            <p className="text-gray-400 italic text-sm">
                                                Your positive reframe will appear here...
                                            </p>
                                        )}
                                    </div>
                                </div>
                            </div>
                        </div>
                    </section>

                    {/* Blog Grid */}
                    <main className="relative z-10 max-w-6xl mx-auto px-6 pb-24">
                        <div className="flex justify-between items-end mb-10">
                            <h2 className="text-3xl font-bold text-gray-800">Latest Stories</h2>
                            <a href="#" className="hidden md:flex items-center gap-1 text-gray-500 hover:text-gray-800 font-semibold transition-colors">
                                View all <Icon name="arrowRight" size={18} />
                            </a>
                        </div>

                        <div className="grid md:grid-cols-3 gap-8">
                            {/* Featured Post (Nano Banana) */}
                            <article className="md:col-span-2 bg-white rounded-3xl shadow-xl overflow-hidden card-hover group border border-gray-100">
                                <div className="h-64 bg-gray-100 relative overflow-hidden">
                                    {/* Mockup of the comic style image */}
                                    <div className="absolute inset-0 bg-[url('https://images.unsplash.com/photo-1620641788421-7a1c342ea42e?ixlib=rb-4.0.3&auto=format&fit=crop&w=1600&q=80')] bg-cover bg-center group-hover:scale-105 transition-transform duration-700"></div>
                                    <div className="absolute inset-0 bg-gradient-to-t from-black/50 to-transparent"></div>
                                    <div className="absolute bottom-6 left-6 text-white">
                                        <span className="bg-yellow-400 text-yellow-900 text-xs font-bold px-3 py-1 rounded-full mb-3 inline-block">Featured Tutorial</span>
                                        <h3 className="text-2xl font-bold">Getting Started with Nano Banana Pro 🍌</h3>
                                    </div>
                                </div>
                                <div className="p-8">
                                    <p className="text-gray-600 mb-6 leading-relaxed">
                                        Ready to turn your world into a watercolor dream? In this comic tutorial, we walk you through the magical interface of Gemini's new image generation model. From "Thinking with 3 Pro" to the final brushstroke, it's easier than you think.
                                    </p>
                                    <div className="flex items-center justify-between">
                                        <div className="flex items-center gap-3">
                                            <div className="w-10 h-10 rounded-full bg-yellow-200 flex items-center justify-center text-yellow-700 font-bold">NB</div>
                                            <div className="text-sm">
                                                <p className="font-bold text-gray-800">Nano B. Team</p>
                                                <p className="text-gray-500">5 min read</p>
                                            </div>
                                        </div>
                                        <button className="text-yellow-600 font-bold hover:text-yellow-700 inline-flex items-center gap-1">
                                            Read Comic <Icon name="arrowRight" size={18} />
                                        </button>
                                    </div>
                                </div>
                            </article>

                            {/* Secondary Posts */}
                            <div className="flex flex-col gap-8">
                                {posts.slice(1).map(post => (
                                    <article key={post.id} className="bg-white p-6 rounded-3xl shadow-lg border border-gray-100 card-hover flex-1 flex flex-col justify-between">
                                        <div>
                                            <div className="flex items-center justify-between mb-4">
                                                <span className={`${post.color} ${post.textColor} px-3 py-1 rounded-lg text-xs font-bold uppercase tracking-wider`}>
                                                    {post.category}
                                                </span>
                                                <Icon name={post.icon} className="text-gray-400" size={20} />
                                            </div>
                                            <h3 className="text-xl font-bold text-gray-800 mb-3 hover:text-yellow-600 transition-colors cursor-pointer">
                                                {post.title}
                                            </h3>
                                            <p className="text-gray-500 text-sm leading-relaxed">
                                                {post.excerpt}
                                            </p>
                                        </div>
                                        <div className="mt-6 pt-6 border-t border-gray-100 flex items-center justify-between text-sm">
                                            <span className="text-gray-400">{post.date}</span>
                                            <a href="#" className="font-bold text-gray-700 hover:text-yellow-600">Read more</a>
                                        </div>
                                    </article>
                                ))}
                            </div>
                        </div>
                    </main>

                    {/* Newsletter Section */}
                    <section className="bg-gray-900 py-20 px-6 relative overflow-hidden">
                        <div className="absolute top-0 right-0 w-64 h-64 bg-gray-800 rounded-full mix-blend-overlay filter blur-3xl opacity-50 -mr-20 -mt-20"></div>
                        <div className="max-w-4xl mx-auto text-center relative z-10">
                            <h2 className="text-3xl md:text-4xl font-bold text-white mb-4">Keep the Good Vibes Coming</h2>
                            <p className="text-gray-400 mb-10">Join our weekly newsletter for creative prompts, wallpaper downloads, and positivity.</p>
                            
                            {!subscribed ? (
                                <form onSubmit={handleSubscribe} className="flex flex-col md:flex-row gap-4 justify-center max-w-lg mx-auto">
                                    <input 
                                        type="email" 
                                        placeholder="your@email.com" 
                                        className="px-6 py-4 rounded-xl bg-gray-800 text-white border border-gray-700 focus:border-yellow-500 focus:outline-none w-full"
                                        value={email}
                                        onChange={(e) => setEmail(e.target.value)}
                                        required
                                    />
                                    <button type="submit" className="bg-yellow-400 text-gray-900 font-bold px-8 py-4 rounded-xl hover:bg-yellow-300 transition-colors shadow-lg shadow-yellow-400/20 whitespace-nowrap">
                                        Join In
                                    </button>
                                </form>
                            ) : (
                                <div className="bg-green-500/20 border border-green-500/50 text-green-200 px-8 py-4 rounded-xl inline-block animate-bounce-short">
                                    ✨ You're on the list! Stay tuned.
                                </div>
                            )}
                        </div>
                    </section>

                    <footer className="bg-gray-900 border-t border-gray-800 text-gray-500 py-12 px-6 text-center text-sm">
                        <div className="flex justify-center gap-6 mb-8">
                            <a href="#" className="hover:text-white transition-colors">Instagram</a>
                            <a href="#" className="hover:text-white transition-colors">Twitter</a>
                            <a href="#" className="hover:text-white transition-colors">Pinterest</a>
                        </div>
                        <p>&copy; 2023 The Sunny Side Blog. All rights reserved. <br/> Designed with 💛 and Nano Banana Pro.</p>
                    </footer>
                </div>
            );
        }

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>



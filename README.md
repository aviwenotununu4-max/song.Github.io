<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Music Library - Musicful Clone</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        .waveform { background: linear-gradient(90deg, #3b82f6, #10b981, #f59e0b); height: 4px; border-radius: 2px; }
        .playing { background: #3b82f6 !important; transform: scale(1.05); }
    </style>
</head>
<body class="bg-gradient-to-br from-gray-900 to-gray-800 text-white min-h-screen p-8">
    <!-- Header -->
    <header class="max-w-6xl mx-auto mb-12">
        <div class="flex justify-between items-center">
            <h1 class="text-4xl font-bold bg-gradient-to-r from-blue-400 to-purple-500 bg-clip-text text-transparent">
                <i class="fas fa-music mr-3"></i>AI Music Library
            </h1>
            <div class="flex items-center space-x-4">
                <input id="search" type="text" placeholder="Search songs..." class="px-4 py-2 bg-gray-800 border border-gray-600 rounded-lg focus:outline-none focus:border-blue-500">
                <select id="filter" class="px-4 py-2 bg-gray-800 border border-gray-600 rounded-lg">
                    <option>All Genres</option>
                    <option>Pop</option>
                    <option>Rock</option>
                    <option>Electronic</option>
                    <option>Hip Hop</option>
                </select>
                <button onclick="toggleTheme()" class="p-2 bg-gray-800 rounded-lg hover:bg-gray-700">
                    <i class="fas fa-moon"></i>
                </button>
                <button onclick="generateSong()" class="px-6 py-2 bg-gradient-to-r from-blue-500 to-purple-500 rounded-lg hover:shadow-lg transition-all">
                    <i class="fas fa-magic mr-2"></i>Generate AI Song
                </button>
            </div>
        </div>
    </header>

    <!-- Songs Grid -->
    <div id="songsGrid" class="max-w-6xl mx-auto grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6"></div>

    <script>
        // Mock song data (like Musicful library)
        let songs = [
            { id: 1, title: 'Epic Adventure', genre: 'Electronic', duration: '3:45', audio: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3', cover: 'https://picsum.photos/300/300?1' },
            { id: 2, title: 'Chill Vibes', genre: 'Pop', duration: '4:12', audio: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3', cover: 'https://picsum.photos/300/300?2' },
            { id: 3, title: 'Rock Anthem', genre: 'Rock', duration: '5:01', audio: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3', cover: 'https://picsum.photos/300/300?3' },
            { id: 4, title: 'Urban Beat', genre: 'Hip Hop', duration: '2:58', audio: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-4.mp3', cover: 'https://picsum.photos/300/300?4' },
            // Add more as needed
        ];
        let currentAudio = null;
        let playingId = null;

        function renderSongs(songsToShow = songs) {
            const grid = document.getElementById('songsGrid');
            grid.innerHTML = songsToShow.map(song => `
                <div class="bg-gray-800 rounded-2xl p-6 hover:shadow-2xl hover:scale-105 transition-all overflow-hidden group" onclick="playSong(${song.id})">
                    <div class="relative">
                        <img src="${song.cover}" alt="${song.title}" class="w-full h-48 object-cover rounded-xl mb-4">
                        <div class="absolute top-2 right-2 opacity-0 group-hover:opacity-100 transition">
                            <i class="fas fa-heart text-red-500 text-xl"></i>
                        </div>
                    </div>
                    <h3 class="font-bold text-xl mb-2 truncate">${song.title}</h3>
                    <p class="text-gray-400 mb-2">${song.genre} • ${song.duration}</p>
                    <div class="waveform mb-4"></div>
                    <button class="w-full bg-gradient-to-r from-blue-500 to-purple-500 py-3 rounded-xl font-bold hover:from-blue-600 transition-all ${playingId === song.id ? 'playing' : ''}">
                        <i class="fas fa-${playingId === song.id ? 'pause' : 'play'} mr-2"></i>
                        ${playingId === song.id ? 'Playing' : 'Play'}
                    </button>
                </div>
            `).join('');
        }

        function playSong(id) {
            if (currentAudio) currentAudio.pause();
            const song = songs.find(s => s.id === id);
            currentAudio = new Audio(song.audio);
            currentAudio.play();
            playingId = id;
            renderSongs(); // Refresh play buttons
        }

        function generateSong() {
            const genres = ['Pop', 'Rock', 'Electronic', 'Hip Hop'];
            const newSong = {
                id: Date.now(),
                title: `AI Generated - ${genres[Math.floor(Math.random()*4)]} Beat`,
                genre: genres[Math.floor(Math.random()*4)],
                duration: `${Math.floor(Math.random()*3)+2}:${Math.floor(Math.random()*60).toString().padStart(2,'0')}`,
                audio: `https://www.soundhelix.com/examples/mp3/SoundHelix-Song-${Math.floor(Math.random()*10)+1}.mp3`,
                cover: `https://picsum.photos/300/300?${Date.now()}`
            };
            songs.unshift(newSong); // Add to top
            renderSongs();
            // TODO: Real AI - fetch from Replicate API
            // Example: fetch('https://api.replicate.com/v1/predictions', { method: 'POST', body: JSON.stringify({version: 'musicgen-model', input: {prompt: 'epic rock'}}) })
        }

        function toggleTheme() {
            document.body.classList.toggle('bg-gradient-to-br');
            document.body.classList.toggle('from-gray-900');
            // Add more theme logic
        }

        // Search & Filter
        document.getElementById('search').addEventListener('input', (e) => {
            const term = e.target.value.toLowerCase();
            const filtered = songs.filter(s => s.title.toLowerCase().includes(term) || s.genre.toLowerCase().includes(term));
            renderSongs(filtered);
        });

        document.getElementById('filter').addEventListener('change', (e) => {
            const genre = e.target.value === 'All Genres' ? '' : e.target.value;
            const filtered = songs.filter(s => !genre || s.genre === genre);
            renderSongs(filtered);
        });

        // Initial render
        renderSongs();
    </script>
</body>
</html>

#QUICK BOOK
<!doctype html>
<html lang="en" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>QuickBook - Travel &amp; Entertainment</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700;800&amp;display=swap" rel="stylesheet">
  <style>
    body {
      box-sizing: border-box;
      font-family: 'Poppins', sans-serif;
    }
    
    .gradient-bg {
      background: linear-gradient(135deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
    }
    
    .card-gradient {
      background: linear-gradient(145deg, rgba(255,255,255,0.1) 0%, rgba(255,255,255,0.05) 100%);
      backdrop-filter: blur(10px);
    }
    
    .movie-gradient {
      background: linear-gradient(135deg, #e94560 0%, #ff6b6b 100%);
    }
    
    .bus-gradient {
      background: linear-gradient(135deg, #00b894 0%, #00cec9 100%);
    }
    
    .train-gradient {
      background: linear-gradient(135deg, #6c5ce7 0%, #a29bfe 100%);
    }
    
    .cab-gradient {
      background: linear-gradient(135deg, #fdcb6e 0%, #f39c12 100%);
    }
    
    .seat {
      transition: all 0.2s ease;
    }
    
    .seat:hover:not(.booked):not(.selected) {
      transform: scale(1.1);
      background: rgba(233, 69, 96, 0.5);
    }
    
    .seat.selected {
      background: #e94560 !important;
      transform: scale(1.05);
    }
    
    .seat.booked {
      background: #4a4a5a !important;
      cursor: not-allowed;
    }
    
    .animate-fade-in {
      animation: fadeIn 0.4s ease-out;
    }
    
    .animate-slide-up {
      animation: slideUp 0.5s ease-out;
    }
    
    @keyframes fadeIn {
      from { opacity: 0; }
      to { opacity: 1; }
    }
    
    @keyframes slideUp {
      from { opacity: 0; transform: translateY(30px); }
      to { opacity: 1; transform: translateY(0); }
    }
    
    .screen {
      background: linear-gradient(180deg, #4a4a5a 0%, transparent 100%);
      border-radius: 50% 50% 0 0;
      transform: perspective(200px) rotateX(-20deg);
    }
    
    .glow-effect {
      box-shadow: 0 0 30px rgba(233, 69, 96, 0.3);
    }
    
    ::-webkit-scrollbar {
      width: 6px;
    }
    
    ::-webkit-scrollbar-track {
      background: rgba(255,255,255,0.1);
      border-radius: 3px;
    }
    
    ::-webkit-scrollbar-thumb {
      background: rgba(233, 69, 96, 0.5);
      border-radius: 3px;
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
  <script src="/_sdk/element_sdk.js" type="text/javascript"></script>
 </head>
 <body class="h-full">
  <div id="app" class="h-full gradient-bg text-white overflow-auto"></div>
  <script>
    // App State
    let state = {
      currentView: 'home',
      bookingType: null,
      selectedMovie: null,
      selectedTheater: null,
      selectedDate: null,
      selectedTime: null,
      selectedSeats: [],
      searchFrom: '',
      searchTo: '',
      travelDate: '',
      selectedBus: null,
      selectedTrain: null,
      cabPickup: '',
      cabDrop: '',
      cabType: null,
      totalAmount: 0
    };

    // Mock Data
    const movies = [
      { id: 1, title: 'Pushpa 2: The Rule', genre: 'Action/Drama', rating: '8.9', language: 'Telugu', poster: '🎬', duration: '2h 45m', certificate: 'UA' },
      { id: 2, title: 'Stree 2', genre: 'Horror/Comedy', rating: '8.5', language: 'Hindi', poster: '👻', duration: '2h 30m', certificate: 'UA' },
      { id: 3, title: 'Kalki 2898 AD', genre: 'Sci-Fi/Action', rating: '8.7', language: 'Telugu', poster: '🚀', duration: '3h 00m', certificate: 'UA' },
      { id: 4, title: 'Fighter', genre: 'Action/Thriller', rating: '7.8', language: 'Hindi', poster: '✈️', duration: '2h 46m', certificate: 'UA' },
      { id: 5, title: 'Animal', genre: 'Action/Drama', rating: '8.1', language: 'Hindi', poster: '🦁', duration: '3h 21m', certificate: 'A' },
      { id: 6, title: 'Dunki', genre: 'Comedy/Drama', rating: '7.5', language: 'Hindi', poster: '🎭', duration: '2h 41m', certificate: 'UA' }
    ];

    const theaters = [
      { id: 1, name: 'PVR Cinemas - Phoenix Mall', location: 'Whitefield', price: 280, facilities: ['Dolby Atmos', 'Recliner', 'Food Court'] },
      { id: 2, name: 'INOX - Forum Mall', location: 'Koramangala', price: 320, facilities: ['IMAX', '4DX', 'Parking'] },
      { id: 3, name: 'Cinepolis - Orion Mall', location: 'Rajajinagar', price: 250, facilities: ['3D', 'Dolby', 'Metro Connected'] }
    ];

    const showtimes = ['09:30 AM', '12:45 PM', '04:00 PM', '07:15 PM', '10:30 PM'];

    const buses = [
      { id: 1, operator: 'VRL Travels', type: 'AC Sleeper', departure: '21:00', arrival: '06:30', duration: '9h 30m', price: 899, rating: '4.5', seats: 32, amenities: ['WiFi', 'Charging', 'Blanket'] },
      { id: 2, operator: 'SRS Travels', type: 'Volvo Multi-Axle', departure: '22:30', arrival: '08:00', duration: '9h 30m', price: 1199, rating: '4.7', seats: 28, amenities: ['WiFi', 'TV', 'Snacks'] },
      { id: 3, operator: 'Orange Travels', type: 'AC Seater', departure: '06:00', arrival: '14:30', duration: '8h 30m', price: 650, rating: '4.2', seats: 40, amenities: ['Charging', 'Water'] },
      { id: 4, operator: 'Greenline', type: 'Luxury Sleeper', departure: '20:00', arrival: '05:30', duration: '9h 30m', price: 1499, rating: '4.8', seats: 24, amenities: ['WiFi', 'TV', 'Meal', 'Blanket'] }
    ];

    const trains = [
      { id: 1, name: 'Rajdhani Express', number: '12951', departure: '16:55', arrival: '08:35', duration: '15h 40m', classes: [{ type: '1A', price: 4500 }, { type: '2A', price: 2800 }, { type: '3A', price: 1900 }] },
      { id: 2, name: 'Shatabdi Express', number: '12027', departure: '06:00', arrival: '12:30', duration: '6h 30m', classes: [{ type: 'CC', price: 1200 }, { type: 'EC', price: 2200 }] },
      { id: 3, name: 'Duronto Express', number: '12267', departure: '23:00', arrival: '14:15', duration: '15h 15m', classes: [{ type: '2A', price: 2600 }, { type: '3A', price: 1750 }, { type: 'SL', price: 650 }] },
      { id: 4, name: 'Vande Bharat', number: '22439', departure: '05:50', arrival: '11:30', duration: '5h 40m', classes: [{ type: 'CC', price: 1500 }, { type: 'EC', price: 2800 }] }
    ];

    const cabTypes = [
      { id: 1, type: 'Mini', icon: '🚗', basePrice: 150, perKm: 12, capacity: '4 seater', eta: '3 min' },
      { id: 2, type: 'Sedan', icon: '🚙', basePrice: 200, perKm: 15, capacity: '4 seater', eta: '5 min' },
      { id: 3, type: 'SUV', icon: '🚐', basePrice: 300, perKm: 20, capacity: '6 seater', eta: '8 min' },
      { id: 4, type: 'Premium', icon: '🏎️', basePrice: 400, perKm: 25, capacity: '4 seater', eta: '10 min' }
    ];

    // Navigation
    function navigate(view, data = {}) {
      state = { ...state, ...data, currentView: view };
      render();
    }

    function goBack() {
      const backMap = {
        'movieList': 'home',
        'movieDetail': 'movieList',
        'theaterSelect': 'movieDetail',
        'seatSelect': 'theaterSelect',
        'busSearch': 'home',
        'busList': 'busSearch',
        'busSeats': 'busList',
        'trainSearch': 'home',
        'trainList': 'trainSearch',
        'trainClass': 'trainList',
        'cabSearch': 'home',
        'cabSelect': 'cabSearch',
        'payment': state.bookingType === 'movie' ? 'seatSelect' : 
                   state.bookingType === 'bus' ? 'busSeats' :
                   state.bookingType === 'train' ? 'trainClass' : 'cabSelect',
        'confirmation': 'home'
      };
      navigate(backMap[state.currentView] || 'home');
    }

    // Render functions
    function render() {
      const app = document.getElementById('app');
      
      switch(state.currentView) {
        case 'home': app.innerHTML = renderHome(); break;
        case 'movieList': app.innerHTML = renderMovieList(); break;
        case 'movieDetail': app.innerHTML = renderMovieDetail(); break;
        case 'theaterSelect': app.innerHTML = renderTheaterSelect(); break;
        case 'seatSelect': app.innerHTML = renderSeatSelect(); break;
        case 'busSearch': app.innerHTML = renderBusSearch(); break;
        case 'busList': app.innerHTML = renderBusList(); break;
        case 'busSeats': app.innerHTML = renderBusSeats(); break;
        case 'trainSearch': app.innerHTML = renderTrainSearch(); break;
        case 'trainList': app.innerHTML = renderTrainList(); break;
        case 'trainClass': app.innerHTML = renderTrainClass(); break;
        case 'cabSearch': app.innerHTML = renderCabSearch(); break;
        case 'cabSelect': app.innerHTML = renderCabSelect(); break;
        case 'payment': app.innerHTML = renderPayment(); break;
        case 'confirmation': app.innerHTML = renderConfirmation(); break;
        default: app.innerHTML = renderHome();
      }
      attachEventListeners();
    }

    function renderHeader(title, showBack = true) {
      return `
        <header class="sticky top-0 z-50 px-4 py-4 flex items-center gap-4" style="background: #1a1a2e; border-bottom: 1px solid rgba(255,255,255,0.1);">
          ${showBack ? `<button onclick="goBack()" class="w-10 h-10 rounded-full flex items-center justify-center hover:bg-white/10 transition-colors">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7"/></svg>
          </button>` : ''}
          <h1 class="text-xl font-semibold flex-1">${title}</h1>
        </header>
      `;
    }

    function renderHome() {
      return `
        <div class="min-h-full animate-fade-in">
          <!-- Hero Section -->
          <div class="relative px-6 pt-12 pb-8">
            <div class="absolute inset-0 overflow-hidden">
              <div class="absolute -top-20 -right-20 w-64 h-64 rounded-full opacity-20" style="background: #e94560; filter: blur(60px);"></div>
              <div class="absolute -bottom-20 -left-20 w-64 h-64 rounded-full opacity-20" style="background: #00b894; filter: blur(60px);"></div>
            </div>
            <div class="relative">
              <h1 class="text-4xl font-bold mb-2">QuickBook</h1>
              <p class="text-gray-400 text-lg">Your One-Stop Booking Platform</p>
            </div>
          </div>

          <!-- Booking Categories -->
          <div class="px-6 pb-8">
            <h2 class="text-lg font-semibold mb-4 text-gray-300">What would you like to book?</h2>
            <div class="grid grid-cols-2 gap-4">
              <button onclick="navigate('movieList', { bookingType: 'movie' })" class="group relative overflow-hidden rounded-2xl p-6 movie-gradient text-white text-left transition-transform hover:scale-105 active:scale-95">
                <div class="absolute inset-0 bg-black/20 group-hover:bg-black/10 transition-colors"></div>
                <div class="relative">
                  <span class="text-4xl mb-3 block">🎬</span>
                  <h3 class="text-xl font-bold">Movies</h3>
                  <p class="text-sm opacity-80">Book tickets now</p>
                </div>
              </button>

              <button onclick="navigate('busSearch', { bookingType: 'bus' })" class="group relative overflow-hidden rounded-2xl p-6 bus-gradient text-white text-left transition-transform hover:scale-105 active:scale-95">
                <div class="absolute inset-0 bg-black/20 group-hover:bg-black/10 transition-colors"></div>
                <div class="relative">
                  <span class="text-4xl mb-3 block">🚌</span>
                  <h3 class="text-xl font-bold">Bus</h3>
                  <p class="text-sm opacity-80">Travel comfortably</p>
                </div>
              </button>

              <button onclick="navigate('trainSearch', { bookingType: 'train' })" class="group relative overflow-hidden rounded-2xl p-6 train-gradient text-white text-left transition-transform hover:scale-105 active:scale-95">
                <div class="absolute inset-0 bg-black/20 group-hover:bg-black/10 transition-colors"></div>
                <div class="relative">
                  <span class="text-4xl mb-3 block">🚆</span>
                  <h3 class="text-xl font-bold">Train</h3>
                  <p class="text-sm opacity-80">Book IRCTC tickets</p>
                </div>
              </button>

              <button onclick="navigate('cabSearch', { bookingType: 'cab' })" class="group relative overflow-hidden rounded-2xl p-6 cab-gradient text-gray-900 text-left transition-transform hover:scale-105 active:scale-95">
                <div class="absolute inset-0 bg-black/10 group-hover:bg-black/0 transition-colors"></div>
                <div class="relative">
                  <span class="text-4xl mb-3 block">🚕</span>
                  <h3 class="text-xl font-bold">Cab</h3>
                  <p class="text-sm opacity-80">Ride anywhere</p>
                </div>
              </button>
            </div>
          </div>

          <!-- Trending Movies -->
          <div class="px-6 pb-8">
            <h2 class="text-lg font-semibold mb-4 text-gray-300">🔥 Trending Movies</h2>
            <div class="flex gap-4 overflow-x-auto pb-4 -mx-6 px-6 scrollbar-hide">
              ${movies.slice(0, 4).map(movie => `
                <button onclick="navigate('movieDetail', { bookingType: 'movie', selectedMovie: ${JSON.stringify(movie).replace(/"/g, '&quot;')} })" class="flex-shrink-0 w-36 group">
                  <div class="w-36 h-52 rounded-xl flex items-center justify-center text-6xl mb-3 transition-transform group-hover:scale-105" style="background: linear-gradient(145deg, #16213e, #1a1a2e);">
                    ${movie.poster}
                  </div>
                  <h3 class="font-medium text-sm truncate">${movie.title}</h3>
                  <p class="text-xs text-gray-400">${movie.genre}</p>
                </button>
              `).join('')}
            </div>
          </div>

          <!-- Quick Actions -->
          <div class="px-6 pb-8">
            <h2 class="text-lg font-semibold mb-4 text-gray-300">Quick Actions</h2>
            <div class="grid grid-cols-4 gap-3">
              <div class="text-center">
                <div class="w-14 h-14 rounded-full mx-auto mb-2 flex items-center justify-center text-2xl" style="background: rgba(233, 69, 96, 0.2);">🎁</div>
                <span class="text-xs text-gray-400">Offers</span>
              </div>
              <div class="text-center">
                <div class="w-14 h-14 rounded-full mx-auto mb-2 flex items-center justify-center text-2xl" style="background: rgba(0, 184, 148, 0.2);">📍</div>
                <span class="text-xs text-gray-400">Nearby</span>
              </div>
              <div class="text-center">
                <div class="w-14 h-14 rounded-full mx-auto mb-2 flex items-center justify-center text-2xl" style="background: rgba(108, 92, 231, 0.2);">🎫</div>
                <span class="text-xs text-gray-400">My Tickets</span>
              </div>
              <div class="text-center">
                <div class="w-14 h-14 rounded-full mx-auto mb-2 flex items-center justify-center text-2xl" style="background: rgba(253, 203, 110, 0.2);">⭐</div>
                <span class="text-xs text-gray-400">Rewards</span>
              </div>
            </div>
          </div>
        </div>
      `;
    }

    function renderMovieList() {
      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader('Movies')}
          
          <!-- Search Bar -->
          <div class="px-4 py-4">
            <div class="relative">
              <input type="text" placeholder="Search movies..." class="w-full px-4 py-3 pl-12 rounded-xl border-none outline-none text-white placeholder-gray-400" style="background: rgba(255,255,255,0.1);">
              <svg class="w-5 h-5 absolute left-4 top-1/2 -translate-y-1/2 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"/></svg>
            </div>
          </div>

          <!-- Filters -->
          <div class="px-4 pb-4 flex gap-2 overflow-x-auto">
            <span class="px-4 py-2 rounded-full text-sm whitespace-nowrap" style="background: #e94560;">All Languages</span>
            <span class="px-4 py-2 rounded-full text-sm whitespace-nowrap bg-white/10">Hindi</span>
            <span class="px-4 py-2 rounded-full text-sm whitespace-nowrap bg-white/10">Telugu</span>
            <span class="px-4 py-2 rounded-full text-sm whitespace-nowrap bg-white/10">Tamil</span>
          </div>

          <!-- Movie Grid -->
          <div class="px-4 pb-8 grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-4">
            ${movies.map(movie => `
              <button onclick='navigate("movieDetail", { selectedMovie: ${JSON.stringify(movie).replace(/'/g, "\\'")} })' class="text-left group animate-slide-up">
                <div class="aspect-[3/4] rounded-xl flex items-center justify-center text-6xl mb-3 relative overflow-hidden transition-transform group-hover:scale-105" style="background: linear-gradient(145deg, #16213e, #1a1a2e);">
                  <span class="text-7xl">${movie.poster}</span>
                  <div class="absolute top-2 right-2 px-2 py-1 rounded text-xs font-bold" style="background: #e94560;">
                    ⭐ ${movie.rating}
                  </div>
                  <div class="absolute bottom-2 left-2 px-2 py-1 rounded text-xs bg-black/60">
                    ${movie.certificate}
                  </div>
                </div>
                <h3 class="font-semibold text-sm truncate mb-1">${movie.title}</h3>
                <p class="text-xs text-gray-400">${movie.genre}</p>
              </button>
            `).join('')}
          </div>
        </div>
      `;
    }

    function renderMovieDetail() {
      const movie = state.selectedMovie;
      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader(movie.title)}
          
          <!-- Movie Hero -->
          <div class="relative">
            <div class="h-64 flex items-center justify-center text-9xl" style="background: linear-gradient(180deg, #16213e 0%, #1a1a2e 100%);">
              ${movie.poster}
            </div>
            <div class="absolute inset-0 bg-gradient-to-t from-[#1a1a2e] via-transparent to-transparent"></div>
          </div>

          <!-- Movie Info -->
          <div class="px-6 -mt-16 relative z-10">
            <div class="flex items-start gap-4 mb-6">
              <div class="w-28 h-40 rounded-xl flex items-center justify-center text-5xl shadow-2xl" style="background: linear-gradient(145deg, #16213e, #1a1a2e); border: 2px solid rgba(255,255,255,0.1);">
                ${movie.poster}
              </div>
              <div class="flex-1 pt-8">
                <h1 class="text-2xl font-bold mb-2">${movie.title}</h1>
                <div class="flex flex-wrap gap-2 mb-3">
                  <span class="px-3 py-1 rounded-full text-xs bg-white/10">${movie.language}</span>
                  <span class="px-3 py-1 rounded-full text-xs bg-white/10">${movie.duration}</span>
                  <span class="px-3 py-1 rounded-full text-xs bg-white/10">${movie.certificate}</span>
                </div>
                <div class="flex items-center gap-2">
                  <span class="text-yellow-400">⭐</span>
                  <span class="font-bold">${movie.rating}/10</span>
                  <span class="text-gray-400 text-sm">• 125K votes</span>
                </div>
              </div>
            </div>

            <!-- Genre Tags -->
            <div class="flex gap-2 mb-6">
              ${movie.genre.split('/').map(g => `
                <span class="px-4 py-2 rounded-full text-sm border border-white/20">${g.trim()}</span>
              `).join('')}
            </div>

            <!-- About -->
            <div class="mb-6">
              <h2 class="font-semibold mb-2">About the movie</h2>
              <p class="text-gray-400 text-sm leading-relaxed">
                An action-packed blockbuster that will keep you on the edge of your seat. Experience the thrill of the big screen with stunning visuals and an incredible soundtrack.
              </p>
            </div>

            <!-- Cast -->
            <div class="mb-8">
              <h2 class="font-semibold mb-3">Cast</h2>
              <div class="flex gap-4 overflow-x-auto pb-2">
                ${['👤 Actor 1', '👤 Actor 2', '👤 Actor 3', '👤 Actor 4'].map(actor => `
                  <div class="flex-shrink-0 text-center">
                    <div class="w-16 h-16 rounded-full bg-white/10 flex items-center justify-center text-2xl mb-2">👤</div>
                    <span class="text-xs text-gray-400">${actor.split(' ')[1]}</span>
                  </div>
                `).join('')}
              </div>
            </div>
          </div>

          <!-- Book Button -->
          <div class="sticky bottom-0 p-4" style="background: linear-gradient(180deg, transparent 0%, #1a1a2e 30%);">
            <button onclick="navigate('theaterSelect')" class="w-full py-4 rounded-xl font-bold text-lg transition-transform hover:scale-105 active:scale-95" style="background: #e94560;">
              Book Tickets
            </button>
          </div>
        </div>
      `;
    }

    function renderTheaterSelect() {
      const dates = [];
      const today = new Date();
      for(let i = 0; i < 7; i++) {
        const date = new Date(today);
        date.setDate(today.getDate() + i);
        dates.push({
          day: date.toLocaleDateString('en-US', { weekday: 'short' }),
          date: date.getDate(),
          month: date.toLocaleDateString('en-US', { month: 'short' }),
          full: date.toISOString().split('T')[0]
        });
      }

      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader('Select Theater')}
          
          <!-- Date Selection -->
          <div class="px-4 py-4">
            <div class="flex gap-2 overflow-x-auto pb-2">
              ${dates.map((d, i) => `
                <button onclick="state.selectedDate = '${d.full}'; render();" class="flex-shrink-0 w-16 py-3 rounded-xl text-center transition-all ${state.selectedDate === d.full || (i === 0 && !state.selectedDate) ? 'scale-105' : 'bg-white/5'}" style="${state.selectedDate === d.full || (i === 0 && !state.selectedDate) ? 'background: #e94560;' : ''}">
                  <div class="text-xs opacity-70">${d.day}</div>
                  <div class="text-xl font-bold">${d.date}</div>
                  <div class="text-xs opacity-70">${d.month}</div>
                </button>
              `).join('')}
            </div>
          </div>

          <!-- Theaters -->
          <div class="px-4 pb-24">
            ${theaters.map(theater => `
              <div class="mb-4 p-4 rounded-xl card-gradient border border-white/10 animate-slide-up">
                <div class="flex justify-between items-start mb-3">
                  <div>
                    <h3 class="font-semibold mb-1">${theater.name}</h3>
                    <p class="text-sm text-gray-400">📍 ${theater.location}</p>
                  </div>
                  <span class="text-xs px-2 py-1 rounded bg-green-500/20 text-green-400">₹${theater.price}</span>
                </div>
                <div class="flex gap-2 mb-4 flex-wrap">
                  ${theater.facilities.map(f => `
                    <span class="text-xs px-2 py-1 rounded-full bg-white/10">${f}</span>
                  `).join('')}
                </div>
                <div class="flex gap-2 flex-wrap">
                  ${showtimes.map(time => `
                    <button onclick='navigate("seatSelect", { selectedTheater: ${JSON.stringify(theater).replace(/'/g, "\\'")}, selectedTime: "${time}" })' class="px-4 py-2 rounded-lg border text-sm font-medium transition-all hover:scale-105" style="border-color: #00b894; color: #00b894;">
                      ${time}
                    </button>
                  `).join('')}
                </div>
              </div>
            `).join('')}
          </div>
        </div>
      `;
    }

    function renderSeatSelect() {
      const rows = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H'];
      const seatsPerRow = 12;
      const bookedSeats = ['A3', 'A4', 'B7', 'C5', 'C6', 'D8', 'E2', 'F10', 'G4', 'H6', 'H7'];
      const price = state.selectedTheater?.price || 280;

      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader('Select Seats')}
          
          <!-- Screen -->
          <div class="px-8 pt-8 pb-4">
            <div class="screen h-8 w-3/4 mx-auto mb-2"></div>
            <p class="text-center text-xs text-gray-400">SCREEN</p>
          </div>

          <!-- Seat Grid -->
          <div class="px-4 pb-4 overflow-x-auto">
            <div class="min-w-max flex flex-col items-center gap-2">
              ${rows.map(row => `
                <div class="flex items-center gap-1">
                  <span class="w-6 text-xs text-gray-400 text-right">${row}</span>
                  <div class="flex gap-1">
                    ${Array.from({length: seatsPerRow}, (_, i) => {
                      const seatId = `${row}${i + 1}`;
                      const isBooked = bookedSeats.includes(seatId);
                      const isSelected = state.selectedSeats.includes(seatId);
                      const gap = i === 2 || i === 9 ? 'ml-4' : '';
                      return `
                        <button 
                          onclick="toggleSeat('${seatId}')" 
                          class="seat w-7 h-7 rounded-t-lg text-xs font-medium ${gap} ${isBooked ? 'booked' : isSelected ? 'selected' : ''}"
                          style="${!isBooked && !isSelected ? 'background: rgba(255,255,255,0.2);' : ''}"
                          ${isBooked ? 'disabled' : ''}>
                          ${i + 1}
                        </button>
                      `;
                    }).join('')}
                  </div>
                </div>
              `).join('')}
            </div>
          </div>

          <!-- Legend -->
          <div class="px-4 py-4 flex justify-center gap-6">
            <div class="flex items-center gap-2">
              <div class="w-6 h-6 rounded-t-lg bg-white/20"></div>
              <span class="text-xs text-gray-400">Available</span>
            </div>
            <div class="flex items-center gap-2">
              <div class="w-6 h-6 rounded-t-lg" style="background: #e94560;"></div>
              <span class="text-xs text-gray-400">Selected</span>
            </div>
            <div class="flex items-center gap-2">
              <div class="w-6 h-6 rounded-t-lg bg-gray-600"></div>
              <span class="text-xs text-gray-400">Booked</span>
            </div>
          </div>

          <!-- Selected Info -->
          ${state.selectedSeats.length > 0 ? `
            <div class="mx-4 p-4 rounded-xl card-gradient border border-white/10">
              <div class="flex justify-between items-center mb-2">
                <span class="text-gray-400">Selected Seats</span>
                <span class="font-semibold">${state.selectedSeats.join(', ')}</span>
              </div>
              <div class="flex justify-between items-center">
                <span class="text-gray-400">Total Amount</span>
                <span class="font-bold text-xl" style="color: #e94560;">₹${state.selectedSeats.length * price}</span>
              </div>
            </div>
          ` : ''}

          <!-- Continue Button -->
          <div class="sticky bottom-0 p-4" style="background: linear-gradient(180deg, transparent 0%, #1a1a2e 30%);">
            <button 
              onclick="proceedToPayment()" 
              class="w-full py-4 rounded-xl font-bold text-lg transition-all ${state.selectedSeats.length > 0 ? 'hover:scale-105 active:scale-95' : 'opacity-50 cursor-not-allowed'}"
              style="background: #e94560;"
              ${state.selectedSeats.length === 0 ? 'disabled' : ''}>
              ${state.selectedSeats.length > 0 ? `Pay ₹${state.selectedSeats.length * price}` : 'Select Seats'}
            </button>
          </div>
        </div>
      `;
    }

    function renderBusSearch() {
      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader('Bus Tickets')}
          
          <div class="px-6 py-8">
            <!-- Search Card -->
            <div class="p-6 rounded-2xl card-gradient border border-white/10 mb-6">
              <div class="space-y-4">
                <!-- From -->
                <div>
                  <label class="text-xs text-gray-400 mb-1 block">FROM</label>
                  <div class="flex items-center gap-3 p-3 rounded-xl bg-white/5">
                    <span class="text-xl">📍</span>
                    <input type="text" id="busFrom" placeholder="Enter departure city" value="${state.searchFrom}" class="bg-transparent border-none outline-none flex-1 text-white placeholder-gray-500">
                  </div>
                </div>

                <!-- Swap Button -->
                <div class="flex justify-center">
                  <button onclick="swapBusLocations()" class="w-10 h-10 rounded-full bg-white/10 flex items-center justify-center hover:bg-white/20 transition-colors">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16V4m0 0L3 8m4-4l4 4m6 0v12m0 0l4-4m-4 4l-4-4"/></svg>
                  </button>
                </div>

                <!-- To -->
                <div>
                  <label class="text-xs text-gray-400 mb-1 block">TO</label>
                  <div class="flex items-center gap-3 p-3 rounded-xl bg-white/5">
                    <span class="text-xl">📍</span>
                    <input type="text" id="busTo" placeholder="Enter arrival city" value="${state.searchTo}" class="bg-transparent border-none outline-none flex-1 text-white placeholder-gray-500">
                  </div>
                </div>

                <!-- Date -->
                <div>
                  <label class="text-xs text-gray-400 mb-1 block">DATE OF JOURNEY</label>
                  <div class="flex items-center gap-3 p-3 rounded-xl bg-white/5">
                    <span class="text-xl">📅</span>
                    <input type="date" id="busDate" value="${state.travelDate}" class="bg-transparent border-none outline-none flex-1 text-white">
                  </div>
                </div>
              </div>
            </div>

            <!-- Popular Routes -->
            <div class="mb-6">
              <h3 class="text-sm font-semibold text-gray-400 mb-3">POPULAR ROUTES</h3>
              <div class="grid grid-cols-2 gap-3">
                ${[
                  { from: 'Bangalore', to: 'Chennai' },
                  { from: 'Mumbai', to: 'Pune' },
                  { from: 'Delhi', to: 'Jaipur' },
                  { from: 'Hyderabad', to: 'Vijayawada' }
                ].map(route => `
                  <button onclick="setRoute('${route.from}', '${route.to}')" class="p-3 rounded-xl bg-white/5 text-left hover:bg-white/10 transition-colors">
                    <div class="text-sm font-medium">${route.from}</div>
                    <div class="text-xs text-gray-400">→ ${route.to}</div>
                  </button>
                `).join('')}
              </div>
            </div>

            <!-- Search Button -->
            <button onclick="searchBuses()" class="w-full py-4 rounded-xl font-bold text-lg bus-gradient text-white hover:scale-105 active:scale-95 transition-transform">
              Search Buses
            </button>
          </div>
        </div>
      `;
    }

    function renderBusList() {
      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader(`${state.searchFrom || 'Bangalore'} → ${state.searchTo || 'Chennai'}`)}
          
          <!-- Route Info -->
          <div class="px-4 py-3 flex items-center justify-between" style="background: rgba(0, 184, 148, 0.1);">
            <span class="text-sm">${state.travelDate || 'Tomorrow'}</span>
            <span class="text-sm text-gray-400">${buses.length} buses found</span>
          </div>

          <!-- Filters -->
          <div class="px-4 py-3 flex gap-2 overflow-x-auto border-b border-white/10">
            <span class="px-3 py-1 rounded-full text-sm whitespace-nowrap bus-gradient text-white">All</span>
            <span class="px-3 py-1 rounded-full text-sm whitespace-nowrap bg-white/10">AC</span>
            <span class="px-3 py-1 rounded-full text-sm whitespace-nowrap bg-white/10">Sleeper</span>
            <span class="px-3 py-1 rounded-full text-sm whitespace-nowrap bg-white/10">Seater</span>
          </div>

          <!-- Bus List -->
          <div class="px-4 py-4 space-y-4 pb-8">
            ${buses.map(bus => `
              <div class="p-4 rounded-xl card-gradient border border-white/10 animate-slide-up">
                <div class="flex justify-between items-start mb-3">
                  <div>
                    <h3 class="font-semibold mb-1">${bus.operator}</h3>
                    <p class="text-sm text-gray-400">${bus.type}</p>
                  </div>
                  <div class="flex items-center gap-1 px-2 py-1 rounded" style="background: rgba(0, 184, 148, 0.2);">
                    <span class="text-yellow-400 text-sm">⭐</span>
                    <span class="text-sm font-medium" style="color: #00b894;">${bus.rating}</span>
                  </div>
                </div>
                
                <div class="flex justify-between items-center mb-3">
                  <div class="text-center">
                    <div class="text-xl font-bold">${bus.departure}</div>
                    <div class="text-xs text-gray-400">${state.searchFrom || 'Bangalore'}</div>
                  </div>
                  <div class="flex-1 px-4">
                    <div class="border-t border-dashed border-gray-600 relative">
                      <span class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-[#1a1a2e] px-2 text-xs text-gray-400">${bus.duration}</span>
                    </div>
                  </div>
                  <div class="text-center">
                    <div class="text-xl font-bold">${bus.arrival}</div>
                    <div class="text-xs text-gray-400">${state.searchTo || 'Chennai'}</div>
                  </div>
                </div>

                <div class="flex gap-2 mb-4 flex-wrap">
                  ${bus.amenities.map(a => `
                    <span class="text-xs px-2 py-1 rounded-full bg-white/10">${a}</span>
                  `).join('')}
                </div>

                <div class="flex justify-between items-center">
                  <div>
                    <span class="text-2xl font-bold" style="color: #00b894;">₹${bus.price}</span>
                    <span class="text-sm text-gray-400 ml-1">per seat</span>
                  </div>
                  <button onclick='navigate("busSeats", { selectedBus: ${JSON.stringify(bus).replace(/'/g, "\\'")} })' class="px-6 py-2 rounded-lg font-semibold transition-transform hover:scale-105 active:scale-95" style="background: #00b894; color: #1a1a2e;">
                    Select Seats
                  </button>
                </div>
              </div>
            `).join('')}
          </div>
        </div>
      `;
    }

    function renderBusSeats() {
      const bus = state.selectedBus;
      const bookedSeats = ['1', '4', '7', '12', '15', '18', '22', '25'];
      
      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader(bus.operator)}
          
          <!-- Bus Info -->
          <div class="px-4 py-4 border-b border-white/10">
            <div class="flex justify-between items-center">
              <div>
                <div class="font-medium">${bus.type}</div>
                <div class="text-sm text-gray-400">${bus.departure} - ${bus.arrival}</div>
              </div>
              <div class="text-right">
                <div class="font-bold" style="color: #00b894;">₹${bus.price}</div>
                <div class="text-xs text-gray-400">per seat</div>
              </div>
            </div>
          </div>

          <!-- Seat Layout -->
          <div class="px-4 py-6">
            <div class="max-w-xs mx-auto">
              <!-- Driver -->
              <div class="flex justify-end mb-4">
                <div class="w-12 h-12 rounded-lg bg-white/10 flex items-center justify-center text-xl">🚗</div>
              </div>

              <!-- Seats Grid - Bus Layout -->
              <div class="p-4 rounded-xl border-2 border-white/20 bg-white/5">
                <div class="grid grid-cols-5 gap-2">
                  ${Array.from({length: 30}, (_, i) => {
                    const seatNum = (i + 1).toString();
                    const isBooked = bookedSeats.includes(seatNum);
                    const isSelected = state.selectedSeats.includes(seatNum);
                    const isAisle = (i + 1) % 5 === 3;
                    
                    if (isAisle) {
                      return `<div class="w-10 h-10"></div>`;
                    }
                    
                    return `
                      <button 
                        onclick="toggleSeat('${seatNum}')" 
                        class="seat w-10 h-10 rounded-lg text-sm font-medium flex items-center justify-center ${isBooked ? 'booked' : isSelected ? 'selected' : ''}"
                        style="${!isBooked && !isSelected ? 'background: rgba(0, 184, 148, 0.3); border: 1px solid rgba(0, 184, 148, 0.5);' : ''}"
                        ${isBooked ? 'disabled' : ''}>
                        ${seatNum}
                      </button>
                    `;
                  }).join('')}
                </div>
              </div>
            </div>
          </div>

          <!-- Legend -->
          <div class="px-4 py-4 flex justify-center gap-6">
            <div class="flex items-center gap-2">
              <div class="w-6 h-6 rounded" style="background: rgba(0, 184, 148, 0.3); border: 1px solid rgba(0, 184, 148, 0.5);"></div>
              <span class="text-xs text-gray-400">Available</span>
            </div>
            <div class="flex items-center gap-2">
              <div class="w-6 h-6 rounded" style="background: #e94560;"></div>
              <span class="text-xs text-gray-400">Selected</span>
            </div>
            <div class="flex items-center gap-2">
              <div class="w-6 h-6 rounded bg-gray-600"></div>
              <span class="text-xs text-gray-400">Booked</span>
            </div>
          </div>

          <!-- Selected Info -->
          ${state.selectedSeats.length > 0 ? `
            <div class="mx-4 p-4 rounded-xl card-gradient border border-white/10">
              <div class="flex justify-between items-center mb-2">
                <span class="text-gray-400">Selected Seats</span>
                <span class="font-semibold">${state.selectedSeats.join(', ')}</span>
              </div>
              <div class="flex justify-between items-center">
                <span class="text-gray-400">Total Amount</span>
                <span class="font-bold text-xl" style="color: #00b894;">₹${state.selectedSeats.length * bus.price}</span>
              </div>
            </div>
          ` : ''}

          <!-- Continue Button -->
          <div class="sticky bottom-0 p-4" style="background: linear-gradient(180deg, transparent 0%, #1a1a2e 30%);">
            <button 
              onclick="proceedToPayment()" 
              class="w-full py-4 rounded-xl font-bold text-lg transition-all ${state.selectedSeats.length > 0 ? 'hover:scale-105 active:scale-95' : 'opacity-50 cursor-not-allowed'}"
              style="background: #00b894; color: #1a1a2e;"
              ${state.selectedSeats.length === 0 ? 'disabled' : ''}>
              ${state.selectedSeats.length > 0 ? `Continue • ₹${state.selectedSeats.length * bus.price}` : 'Select Seats'}
            </button>
          </div>
        </div>
      `;
    }

    function renderTrainSearch() {
      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader('Train Tickets')}
          
          <div class="px-6 py-8">
            <!-- Search Card -->
            <div class="p-6 rounded-2xl card-gradient border border-white/10 mb-6">
              <div class="space-y-4">
                <div>
                  <label class="text-xs text-gray-400 mb-1 block">FROM STATION</label>
                  <div class="flex items-center gap-3 p-3 rounded-xl bg-white/5">
                    <span class="text-xl">🚉</span>
                    <input type="text" id="trainFrom" placeholder="Enter departure station" value="${state.searchFrom}" class="bg-transparent border-none outline-none flex-1 text-white placeholder-gray-500">
                  </div>
                </div>

                <div class="flex justify-center">
                  <button onclick="swapTrainLocations()" class="w-10 h-10 rounded-full bg-white/10 flex items-center justify-center hover:bg-white/20 transition-colors">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16V4m0 0L3 8m4-4l4 4m6 0v12m0 0l4-4m-4 4l-4-4"/></svg>
                  </button>
                </div>

                <div>
                  <label class="text-xs text-gray-400 mb-1 block">TO STATION</label>
                  <div class="flex items-center gap-3 p-3 rounded-xl bg-white/5">
                    <span class="text-xl">🚉</span>
                    <input type="text" id="trainTo" placeholder="Enter arrival station" value="${state.searchTo}" class="bg-transparent border-none outline-none flex-1 text-white placeholder-gray-500">
                  </div>
                </div>

                <div>
                  <label class="text-xs text-gray-400 mb-1 block">DATE OF JOURNEY</label>
                  <div class="flex items-center gap-3 p-3 rounded-xl bg-white/5">
                    <span class="text-xl">📅</span>
                    <input type="date" id="trainDate" value="${state.travelDate}" class="bg-transparent border-none outline-none flex-1 text-white">
                  </div>
                </div>
              </div>
            </div>

            <!-- Popular Routes -->
            <div class="mb-6">
              <h3 class="text-sm font-semibold text-gray-400 mb-3">POPULAR ROUTES</h3>
              <div class="grid grid-cols-2 gap-3">
                ${[
                  { from: 'New Delhi', to: 'Mumbai' },
                  { from: 'Chennai', to: 'Bangalore' },
                  { from: 'Kolkata', to: 'Delhi' },
                  { from: 'Mumbai', to: 'Ahmedabad' }
                ].map(route => `
                  <button onclick="setTrainRoute('${route.from}', '${route.to}')" class="p-3 rounded-xl bg-white/5 text-left hover:bg-white/10 transition-colors">
                    <div class="text-sm font-medium">${route.from}</div>
                    <div class="text-xs text-gray-400">→ ${route.to}</div>
                  </button>
                `).join('')}
              </div>
            </div>

            <button onclick="searchTrains()" class="w-full py-4 rounded-xl font-bold text-lg train-gradient text-white hover:scale-105 active:scale-95 transition-transform">
              Search Trains
            </button>
          </div>
        </div>
      `;
    }

    function renderTrainList() {
      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader(`${state.searchFrom || 'New Delhi'} → ${state.searchTo || 'Mumbai'}`)}
          
          <div class="px-4 py-3 flex items-center justify-between" style="background: rgba(108, 92, 231, 0.1);">
            <span class="text-sm">${state.travelDate || 'Tomorrow'}</span>
            <span class="text-sm text-gray-400">${trains.length} trains found</span>
          </div>

          <div class="px-4 py-4 space-y-4 pb-8">
            ${trains.map(train => `
              <div class="p-4 rounded-xl card-gradient border border-white/10 animate-slide-up">
                <div class="flex justify-between items-start mb-3">
                  <div>
                    <h3 class="font-semibold mb-1">${train.name}</h3>
                    <p class="text-sm text-gray-400">#${train.number}</p>
                  </div>
                  <div class="flex gap-1">
                    ${['M', 'T', 'W', 'T', 'F', 'S', 'S'].map((d, i) => `
                      <span class="w-5 h-5 rounded text-xs flex items-center justify-center ${i < 5 ? 'bg-green-500/30 text-green-400' : 'bg-white/10 text-gray-500'}">${d}</span>
                    `).join('')}
                  </div>
                </div>
                
                <div class="flex justify-between items-center mb-4">
                  <div class="text-center">
                    <div class="text-xl font-bold">${train.departure}</div>
                    <div class="text-xs text-gray-400">${state.searchFrom || 'NDLS'}</div>
                  </div>
                  <div class="flex-1 px-4">
                    <div class="border-t border-dashed border-gray-600 relative">
                      <span class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 px-2 text-xs text-gray-400" style="background: #16213e;">${train.duration}</span>
                    </div>
                  </div>
                  <div class="text-center">
                    <div class="text-xl font-bold">${train.arrival}</div>
                    <div class="text-xs text-gray-400">${state.searchTo || 'CSTM'}</div>
                  </div>
                </div>

                <div class="flex gap-2 flex-wrap">
                  ${train.classes.map(cls => `
                    <button onclick='navigate("trainClass", { selectedTrain: ${JSON.stringify({...train, selectedClass: cls}).replace(/'/g, "\\'")} })' class="flex-1 min-w-[80px] p-3 rounded-lg border text-center transition-all hover:scale-105" style="border-color: rgba(108, 92, 231, 0.5);">
                      <div class="text-sm font-semibold" style="color: #a29bfe;">${cls.type}</div>
                      <div class="text-xs text-gray-400">₹${cls.price}</div>
                      <div class="text-xs text-green-400 mt-1">AVL 45</div>
                    </button>
                  `).join('')}
                </div>
              </div>
            `).join('')}
          </div>
        </div>
      `;
    }

    function renderTrainClass() {
      const train = state.selectedTrain;
      const cls = train.selectedClass;
      
      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader(train.name)}
          
          <div class="px-4 py-6">
            <!-- Train Info Card -->
            <div class="p-4 rounded-xl card-gradient border border-white/10 mb-6">
              <div class="flex justify-between items-center mb-4">
                <div class="text-center">
                  <div class="text-2xl font-bold">${train.departure}</div>
                  <div class="text-sm text-gray-400">${state.searchFrom || 'New Delhi'}</div>
                </div>
                <div class="flex-1 px-4 text-center">
                  <div class="text-sm text-gray-400">${train.duration}</div>
                  <div class="border-t border-dashed border-gray-600 mt-2"></div>
                </div>
                <div class="text-center">
                  <div class="text-2xl font-bold">${train.arrival}</div>
                  <div class="text-sm text-gray-400">${state.searchTo || 'Mumbai'}</div>
                </div>
              </div>
              
              <div class="flex justify-between items-center p-3 rounded-lg" style="background: rgba(108, 92, 231, 0.2);">
                <span class="font-medium">${cls.type} Class</span>
                <span class="text-xl font-bold" style="color: #a29bfe;">₹${cls.price}</span>
              </div>
            </div>

            <!-- Passenger Details -->
            <div class="mb-6">
              <h3 class="font-semibold mb-3">Passenger Details</h3>
              <div class="space-y-4">
                <div class="p-4 rounded-xl bg-white/5">
                  <label class="text-xs text-gray-400 block mb-2">PASSENGER NAME</label>
                  <input type="text" placeholder="Enter passenger name" class="w-full bg-transparent border-none outline-none text-white placeholder-gray-500">
                </div>
                <div class="grid grid-cols-2 gap-4">
                  <div class="p-4 rounded-xl bg-white/5">
                    <label class="text-xs text-gray-400 block mb-2">AGE</label>
                    <input type="number" placeholder="Age" class="w-full bg-transparent border-none outline-none text-white placeholder-gray-500">
                  </div>
                  <div class="p-4 rounded-xl bg-white/5">
                    <label class="text-xs text-gray-400 block mb-2">GENDER</label>
                    <select class="w-full bg-transparent border-none outline-none text-white">
                      <option value="" class="bg-gray-800">Select</option>
                      <option value="M" class="bg-gray-800">Male</option>
                      <option value="F" class="bg-gray-800">Female</option>
                      <option value="O" class="bg-gray-800">Other</option>
                    </select>
                  </div>
                </div>
                <div class="p-4 rounded-xl bg-white/5">
                  <label class="text-xs text-gray-400 block mb-2">BERTH PREFERENCE</label>
                  <div class="flex gap-2 flex-wrap">
                    ${['Lower', 'Middle', 'Upper', 'Side Lower', 'Side Upper'].map(b => `
                      <button class="px-3 py-2 rounded-lg text-sm transition-colors bg-white/10 hover:bg-white/20">${b}</button>
                    `).join('')}
                  </div>
                </div>
              </div>
            </div>

            <!-- Add Passenger -->
            <button class="w-full py-3 rounded-xl border border-dashed border-white/30 text-gray-400 mb-6 hover:border-white/50 transition-colors">
              + Add Another Passenger
            </button>

            <!-- Fare Breakdown -->
            <div class="p-4 rounded-xl card-gradient border border-white/10 mb-6">
              <h3 class="font-semibold mb-3">Fare Breakdown</h3>
              <div class="space-y-2 text-sm">
                <div class="flex justify-between">
                  <span class="text-gray-400">Base Fare</span>
                  <span>₹${cls.price}</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-400">Reservation Charges</span>
                  <span>₹40</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-400">GST</span>
                  <span>₹${Math.round(cls.price * 0.05)}</span>
                </div>
                <div class="border-t border-white/10 pt-2 flex justify-between font-bold">
                  <span>Total</span>
                  <span style="color: #a29bfe;">₹${cls.price + 40 + Math.round(cls.price * 0.05)}</span>
                </div>
              </div>
            </div>
          </div>

          <!-- Continue Button -->
          <div class="sticky bottom-0 p-4" style="background: linear-gradient(180deg, transparent 0%, #1a1a2e 30%);">
            <button onclick="state.totalAmount = ${cls.price + 40 + Math.round(cls.price * 0.05)}; navigate('payment')" class="w-full py-4 rounded-xl font-bold text-lg train-gradient text-white hover:scale-105 active:scale-95 transition-transform">
              Proceed to Pay • ₹${cls.price + 40 + Math.round(cls.price * 0.05)}
            </button>
          </div>
        </div>
      `;
    }

    function renderCabSearch() {
      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader('Book a Cab')}
          
          <div class="px-6 py-8">
            <!-- Map Placeholder -->
            <div class="h-48 rounded-2xl mb-6 flex items-center justify-center relative overflow-hidden" style="background: linear-gradient(145deg, #16213e, #1a1a2e);">
              <div class="text-6xl">🗺️</div>
              <div class="absolute bottom-4 left-4 right-4 text-center text-sm text-gray-400">
                Map view would appear here
              </div>
            </div>

            <!-- Location Inputs -->
            <div class="p-4 rounded-2xl card-gradient border border-white/10 mb-6">
              <div class="space-y-3">
                <div class="flex items-center gap-3">
                  <div class="w-3 h-3 rounded-full" style="background: #00b894;"></div>
                  <input type="text" id="cabPickup" placeholder="Enter pickup location" value="${state.cabPickup}" class="flex-1 bg-transparent border-none outline-none text-white placeholder-gray-500">
                  <button class="text-sm" style="color: #e94560;">📍 Current</button>
                </div>
                <div class="border-l-2 border-dashed border-gray-600 h-6 ml-1"></div>
                <div class="flex items-center gap-3">
                  <div class="w-3 h-3 rounded-full" style="background: #e94560;"></div>
                  <input type="text" id="cabDrop" placeholder="Enter drop location" value="${state.cabDrop}" class="flex-1 bg-transparent border-none outline-none text-white placeholder-gray-500">
                </div>
              </div>
            </div>

            <!-- Saved Places -->
            <div class="mb-6">
              <h3 class="text-sm font-semibold text-gray-400 mb-3">SAVED PLACES</h3>
              <div class="space-y-3">
                ${[
                  { icon: '🏠', name: 'Home', address: 'Whitefield, Bangalore' },
                  { icon: '💼', name: 'Office', address: 'Koramangala, Bangalore' },
                  { icon: '✈️', name: 'Airport', address: 'Kempegowda International' }
                ].map(place => `
                  <button onclick="setCabDrop('${place.address}')" class="w-full flex items-center gap-4 p-3 rounded-xl bg-white/5 hover:bg-white/10 transition-colors text-left">
                    <span class="text-2xl">${place.icon}</span>
                    <div>
                      <div class="font-medium">${place.name}</div>
                      <div class="text-sm text-gray-400">${place.address}</div>
                    </div>
                  </button>
                `).join('')}
              </div>
            </div>

            <button onclick="searchCabs()" class="w-full py-4 rounded-xl font-bold text-lg cab-gradient text-gray-900 hover:scale-105 active:scale-95 transition-transform">
              Find Cabs
            </button>
          </div>
        </div>
      `;
    }

    function renderCabSelect() {
      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader('Choose Your Ride')}
          
          <!-- Route Info -->
          <div class="px-4 py-4 border-b border-white/10">
            <div class="flex items-center gap-3">
              <div class="flex flex-col items-center">
                <div class="w-3 h-3 rounded-full" style="background: #00b894;"></div>
                <div class="w-0.5 h-8 bg-gray-600"></div>
                <div class="w-3 h-3 rounded-full" style="background: #e94560;"></div>
              </div>
              <div class="flex-1">
                <div class="text-sm mb-2">${state.cabPickup || 'Current Location'}</div>
                <div class="text-sm text-gray-400">${state.cabDrop || 'Destination'}</div>
              </div>
              <div class="text-right">
                <div class="text-sm font-medium">~12 km</div>
                <div class="text-xs text-gray-400">25 min</div>
              </div>
            </div>
          </div>

          <!-- Cab Options -->
          <div class="px-4 py-4 space-y-4">
            ${cabTypes.map(cab => {
              const totalPrice = cab.basePrice + (cab.perKm * 12);
              const isSelected = state.cabType?.id === cab.id;
              return `
                <button onclick='selectCab(${JSON.stringify(cab).replace(/'/g, "\\'")})' class="w-full p-4 rounded-xl transition-all ${isSelected ? 'scale-105' : ''}" style="background: ${isSelected ? 'rgba(253, 203, 110, 0.2)' : 'rgba(255,255,255,0.05)'}; border: 2px solid ${isSelected ? '#fdcb6e' : 'transparent'};">
                  <div class="flex items-center gap-4">
                    <span class="text-4xl">${cab.icon}</span>
                    <div class="flex-1 text-left">
                      <div class="font-semibold">${cab.type}</div>
                      <div class="text-sm text-gray-400">${cab.capacity} • ${cab.eta} away</div>
                    </div>
                    <div class="text-right">
                      <div class="font-bold text-lg" style="color: ${isSelected ? '#fdcb6e' : 'white'};">₹${totalPrice}</div>
                      <div class="text-xs text-gray-400">₹${cab.perKm}/km</div>
                    </div>
                  </div>
                </button>
              `;
            }).join('')}
          </div>

          <!-- Promo Code -->
          <div class="px-4 py-4">
            <div class="flex items-center gap-3 p-3 rounded-xl bg-white/5">
              <span class="text-xl">🎁</span>
              <input type="text" placeholder="Enter promo code" class="flex-1 bg-transparent border-none outline-none text-white placeholder-gray-500">
              <button class="text-sm font-semibold" style="color: #e94560;">APPLY</button>
            </div>
          </div>

          <!-- Payment Method -->
          <div class="px-4 py-4">
            <h3 class="text-sm font-semibold text-gray-400 mb-3">PAYMENT METHOD</h3>
            <div class="flex gap-3">
              <button class="flex-1 p-3 rounded-xl text-center transition-all" style="background: rgba(253, 203, 110, 0.2); border: 1px solid #fdcb6e;">
                <span class="text-2xl block mb-1">💳</span>
                <span class="text-xs">Card</span>
              </button>
              <button class="flex-1 p-3 rounded-xl bg-white/5 text-center">
                <span class="text-2xl block mb-1">💵</span>
                <span class="text-xs">Cash</span>
              </button>
              <button class="flex-1 p-3 rounded-xl bg-white/5 text-center">
                <span class="text-2xl block mb-1">📱</span>
                <span class="text-xs">UPI</span>
              </button>
            </div>
          </div>

          <!-- Book Button -->
          <div class="sticky bottom-0 p-4" style="background: linear-gradient(180deg, transparent 0%, #1a1a2e 30%);">
            <button 
              onclick="bookCab()"
              class="w-full py-4 rounded-xl font-bold text-lg transition-all ${state.cabType ? 'hover:scale-105 active:scale-95' : 'opacity-50 cursor-not-allowed'}"
              style="background: linear-gradient(135deg, #fdcb6e 0%, #f39c12 100%); color: #1a1a2e;"
              ${!state.cabType ? 'disabled' : ''}>
              ${state.cabType ? `Book ${state.cabType.type} • ₹${state.cabType.basePrice + (state.cabType.perKm * 12)}` : 'Select a Cab'}
            </button>
          </div>
        </div>
      `;
    }

    function renderPayment() {
      const gradients = {
        movie: 'movie-gradient',
        bus: 'bus-gradient',
        train: 'train-gradient',
        cab: 'cab-gradient'
      };
      
      return `
        <div class="min-h-full animate-fade-in">
          ${renderHeader('Payment')}
          
          <div class="px-6 py-6">
            <!-- Amount Card -->
            <div class="p-6 rounded-2xl ${gradients[state.bookingType]} text-white text-center mb-6">
              <p class="text-sm opacity-80 mb-2">Total Amount</p>
              <p class="text-4xl font-bold">₹${state.totalAmount}</p>
            </div>

            <!-- Payment Methods -->
            <div class="mb-6">
              <h3 class="font-semibold mb-4">Select Payment Method</h3>
              <div class="space-y-3">
                ${[
                  { icon: '📱', name: 'UPI', desc: 'Google Pay, PhonePe, Paytm' },
                  { icon: '💳', name: 'Credit/Debit Card', desc: 'Visa, Mastercard, RuPay' },
                  { icon: '🏦', name: 'Net Banking', desc: 'All major banks' },
                  { icon: '💰', name: 'Wallets', desc: 'Paytm, Amazon Pay' }
                ].map((method, i) => `
                  <button class="w-full flex items-center gap-4 p-4 rounded-xl transition-all hover:scale-102 ${i === 0 ? '' : 'bg-white/5'}" style="${i === 0 ? 'background: rgba(233, 69, 96, 0.2); border: 2px solid #e94560;' : ''}">
                    <span class="text-2xl">${method.icon}</span>
                    <div class="text-left flex-1">
                      <div class="font-medium">${method.name}</div>
                      <div class="text-sm text-gray-400">${method.desc}</div>
                    </div>
                    <div class="w-5 h-5 rounded-full border-2 flex items-center justify-center" style="border-color: ${i === 0 ? '#e94560' : 'rgba(255,255,255,0.3)'};">
                      ${i === 0 ? '<div class="w-3 h-3 rounded-full" style="background: #e94560;"></div>' : ''}
                    </div>
                  </button>
                `).join('')}
              </div>
            </div>

            <!-- UPI Input -->
            <div class="mb-6">
              <label class="text-sm text-gray-400 mb-2 block">Enter UPI ID</label>
              <div class="flex gap-2">
                <input type="text" placeholder="username@upi" class="flex-1 px-4 py-3 rounded-xl bg-white/5 border-none outline-none text-white placeholder-gray-500">
                <button class="px-4 py-3 rounded-xl bg-white/10 text-sm font-medium">Verify</button>
              </div>
            </div>

            <!-- Offers -->
            <div class="p-4 rounded-xl border border-dashed border-green-500/50 mb-6" style="background: rgba(0, 184, 148, 0.1);">
              <div class="flex items-center gap-3">
                <span class="text-2xl">🎉</span>
                <div class="flex-1">
                  <div class="font-medium text-green-400">FLAT ₹50 OFF</div>
                  <div class="text-sm text-gray-400">Use code FIRST50</div>
                </div>
                <button class="text-sm font-semibold" style="color: #00b894;">APPLY</button>
              </div>
            </div>
          </div>

          <!-- Pay Button -->
          <div class="sticky bottom-0 p-4" style="background: linear-gradient(180deg, transparent 0%, #1a1a2e 30%);">
            <button onclick="completePayment()" class="w-full py-4 rounded-xl font-bold text-lg ${gradients[state.bookingType]} text-white hover:scale-105 active:scale-95 transition-transform">
              Pay ₹${state.totalAmount}
            </button>
          </div>
        </div>
      `;
    }

    function renderConfirmation() {
      const icons = { movie: '🎬', bus: '🚌', train: '🚆', cab: '🚕' };
      const titles = { movie: 'Movie Ticket', bus: 'Bus Ticket', train: 'Train Ticket', cab: 'Cab Booking' };
      const gradients = { movie: 'movie-gradient', bus: 'bus-gradient', train: 'train-gradient', cab: 'cab-gradient' };
      
      return `
        <div class="min-h-full animate-fade-in flex flex-col items-center justify-center px-6 py-8">
          <!-- Success Animation -->
          <div class="w-24 h-24 rounded-full flex items-center justify-center mb-6 ${gradients[state.bookingType]} glow-effect">
            <svg class="w-12 h-12 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7"/>
            </svg>
          </div>

          <h1 class="text-2xl font-bold mb-2">Booking Confirmed!</h1>
          <p class="text-gray-400 mb-8">Your ${titles[state.bookingType]} has been booked successfully</p>

          <!-- Ticket Card -->
          <div class="w-full max-w-sm p-6 rounded-2xl card-gradient border border-white/10 mb-8">
            <div class="text-center mb-6">
              <span class="text-5xl">${icons[state.bookingType]}</span>
            </div>
            
            <div class="space-y-4">
              <div class="flex justify-between">
                <span class="text-gray-400">Booking ID</span>
                <span class="font-mono font-bold">#QB${Date.now().toString().slice(-8)}</span>
              </div>
              
              ${state.bookingType === 'movie' ? `
                <div class="flex justify-between">
                  <span class="text-gray-400">Movie</span>
                  <span class="font-medium">${state.selectedMovie?.title || 'N/A'}</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-400">Theater</span>
                  <span class="font-medium">${state.selectedTheater?.name?.split('-')[0] || 'N/A'}</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-400">Seats</span>
                  <span class="font-medium">${state.selectedSeats.join(', ') || 'N/A'}</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-400">Show Time</span>
                  <span class="font-medium">${state.selectedTime || 'N/A'}</span>
                </div>
              ` : state.bookingType === 'bus' ? `
                <div class="flex justify-between">
                  <span class="text-gray-400">Operator</span>
                  <span class="font-medium">${state.selectedBus?.operator || 'N/A'}</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-400">Route</span>
                  <span class="font-medium">${state.searchFrom} → ${state.searchTo}</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-400">Seats</span>
                  <span class="font-medium">${state.selectedSeats.join(', ') || 'N/A'}</span>
                </div>
              ` : state.bookingType === 'train' ? `
                <div class="flex justify-between">
                  <span class="text-gray-400">Train</span>
                  <span class="font-medium">${state.selectedTrain?.name || 'N/A'}</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-400">Route</span>
                  <span class="font-medium">${state.searchFrom} → ${state.searchTo}</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-400">Class</span>
                  <span class="font-medium">${state.selectedTrain?.selectedClass?.type || 'N/A'}</span>
                </div>
              ` : `
                <div class="flex justify-between">
                  <span class="text-gray-400">Cab Type</span>
                  <span class="font-medium">${state.cabType?.type || 'N/A'}</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-400">Pickup</span>
                  <span class="font-medium">${state.cabPickup || 'Current Location'}</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-400">Drop</span>
                  <span class="font-medium">${state.cabDrop || 'Destination'}</span>
                </div>
              `}
              
              <div class="border-t border-dashed border-white/20 pt-4 flex justify-between">
                <span class="text-gray-400">Amount Paid</span>
                <span class="font-bold text-xl" style="color: #e94560;">₹${state.totalAmount}</span>
              </div>
            </div>
          </div>

          <!-- Actions -->
          <div class="w-full max-w-sm space-y-3">
            <button class="w-full py-3 rounded-xl font-semibold bg-white/10 hover:bg-white/20 transition-colors">
              📄 Download Ticket
            </button>
            <button onclick="resetAndGoHome()" class="w-full py-3 rounded-xl font-semibold ${gradients[state.bookingType]} text-white hover:scale-105 active:scale-95 transition-transform">
              Book Another
            </button>
          </div>
        </div>
      `;
    }

    // Helper Functions
    function toggleSeat(seatId) {
      const index = state.selectedSeats.indexOf(seatId);
      if (index > -1) {
        state.selectedSeats.splice(index, 1);
      } else {
        state.selectedSeats.push(seatId);
      }
      render();
    }

    function proceedToPayment() {
      if (state.selectedSeats.length === 0) return;
      
      let price = 0;
      if (state.bookingType === 'movie') {
        price = state.selectedTheater?.price || 280;
      } else if (state.bookingType === 'bus') {
        price = state.selectedBus?.price || 899;
      }
      
      state.totalAmount = state.selectedSeats.length * price;
      navigate('payment');
    }

    function completePayment() {
      navigate('confirmation');
    }

    function resetAndGoHome() {
      state = {
        currentView: 'home',
        bookingType: null,
        selectedMovie: null,
        selectedTheater: null,
        selectedDate: null,
        selectedTime: null,
        selectedSeats: [],
        searchFrom: '',
        searchTo: '',
        travelDate: '',
        selectedBus: null,
        selectedTrain: null,
        cabPickup: '',
        cabDrop: '',
        cabType: null,
        totalAmount: 0
      };
      render();
    }

    function setRoute(from, to) {
      state.searchFrom = from;
      state.searchTo = to;
      render();
    }

    function setTrainRoute(from, to) {
      state.searchFrom = from;
      state.searchTo = to;
      render();
    }

    function searchBuses() {
      const fromInput = document.getElementById('busFrom');
      const toInput = document.getElementById('busTo');
      const dateInput = document.getElementById('busDate');
      
      state.searchFrom = fromInput?.value || state.searchFrom || 'Bangalore';
      state.searchTo = toInput?.value || state.searchTo || 'Chennai';
      state.travelDate = dateInput?.value || state.travelDate;
      state.selectedSeats = [];
      
      navigate('busList');
    }

    function searchTrains() {
      const fromInput = document.getElementById('trainFrom');
      const toInput = document.getElementById('trainTo');
      const dateInput = document.getElementById('trainDate');
      
      state.searchFrom = fromInput?.value || state.searchFrom || 'New Delhi';
      state.searchTo = toInput?.value || state.searchTo || 'Mumbai';
      state.travelDate = dateInput?.value || state.travelDate;
      
      navigate('trainList');
    }

    function swapBusLocations() {
      const temp = state.searchFrom;
      state.searchFrom = state.searchTo;
      state.searchTo = temp;
      render();
    }

    function swapTrainLocations() {
      const temp = state.searchFrom;
      state.searchFrom = state.searchTo;
      state.searchTo = temp;
      render();
    }

    function setCabDrop(location) {
      state.cabDrop = location;
      render();
    }

    function searchCabs() {
      const pickupInput = document.getElementById('cabPickup');
      const dropInput = document.getElementById('cabDrop');
      
      state.cabPickup = pickupInput?.value || state.cabPickup || 'Current Location';
      state.cabDrop = dropInput?.value || state.cabDrop || 'Destination';
      
      navigate('cabSelect');
    }

    function selectCab(cab) {
      state.cabType = cab;
      render();
    }

    function bookCab() {
      if (!state.cabType) return;
      state.totalAmount = state.cabType.basePrice + (state.cabType.perKm * 12);
      navigate('payment');
    }

    function attachEventListeners() {
      // Event listeners attached via onclick attributes
    }

    // Make functions globally available
    window.navigate = navigate;
    window.goBack = goBack;
    window.toggleSeat = toggleSeat;
    window.proceedToPayment = proceedToPayment;
    window.completePayment = completePayment;
    window.resetAndGoHome = resetAndGoHome;
    window.setRoute = setRoute;
    window.setTrainRoute = setTrainRoute;
    window.searchBuses = searchBuses;
    window.searchTrains = searchTrains;
    window.swapBusLocations = swapBusLocations;
    window.swapTrainLocations = swapTrainLocations;
    window.setCabDrop = setCabDrop;
    window.searchCabs = searchCabs;
    window.selectCab = selectCab;
    window.bookCab = bookCab;

    // Initial render
    render();
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9c0ec43313cc3c12',t:'MTc2ODkxNDE0OC4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>

# youtube-cloan
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>StreamHub - Video Sharing Platform</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        /* Custom animations and transitions */
        .video-card {
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .video-card:hover {
            transform: translateY(-4px);
        }
        .modal-backdrop {
            backdrop-filter: blur(8px);
            background-color: rgba(0, 0, 0, 0.7);
        }
        .category-pill {
            transition: all 0.2s ease;
        }
        .category-pill:hover {
            transform: scale(1.05);
        }
        .category-pill.active {
            background-color: #000;
            color: white;
        }
        .search-input:focus {
            box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
        }
        .duration-badge {
            background: rgba(0, 0, 0, 0.8);
            backdrop-filter: blur(4px);
        }
        .comment-item {
            animation: slideIn 0.3s ease-out;
        }
        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateX(-20px);
            }
            to {
                opacity: 1;
                transform: translateX(0);
            }
        }
        .upload-area {
            border: 2px dashed #cbd5e1;
            transition: all 0.3s ease;
        }
        .upload-area:hover {
            border-color: #3b82f6;
            background-color: #f8fafc;
        }
        .upload-area.dragover {
            border-color: #3b82f6;
            background-color: #eff6ff;
        }
    </style>
</head>
<body class="bg-gray-50">
    <!-- Navigation Bar -->
    <nav class="bg-white shadow-sm sticky top-0 z-40">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-16">
                <!-- Branding -->
                <div class="flex items-center">
                    <button id="menuToggle" class="lg:hidden p-2 rounded-md hover:bg-gray-100">
                        <i class="fas fa-bars text-gray-600"></i>
                    </button>
                    <div class="flex items-center ml-2 lg:ml-0">
                        <i class="fas fa-play-circle text-red-600 text-3xl mr-2"></i>
                        <span class="text-xl font-bold text-gray-900">StreamHub</span>
                    </div>
                </div>

                <!-- Search Bar -->
                <div class="flex-1 max-w-2xl mx-4">
                    <div class="relative">
                        <input 
                            type="text" 
                            id="searchInput"
                            placeholder="Search videos..."
                            class="search-input w-full px-4 py-2 pl-10 pr-4 border border-gray-300 rounded-full focus:outline-none focus:border-blue-500 transition-all"
                        >
                        <i class="fas fa-search absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400"></i>
                    </div>
                </div>

                <!-- User Actions -->
                <div class="flex items-center space-x-4">
                    <button id="uploadBtn" class="bg-blue-600 text-white px-4 py-2 rounded-full hover:bg-blue-700 transition-colors flex items-center">
                        <i class="fas fa-upload mr-2"></i>
                        <span class="hidden sm:inline">Upload</span>
                    </button>
                    <button class="p-2 rounded-full hover:bg-gray-100 relative">
                        <i class="fas fa-bell text-gray-600"></i>
                        <span class="absolute top-0 right-0 h-2 w-2 bg-red-600 rounded-full"></span>
                    </button>
                    <div class="relative">
                        <button id="profileBtn" class="w-10 h-10 rounded-full overflow-hidden border-2 border-gray-300 hover:border-blue-500 transition-colors">
                            <img src="https://picsum.photos/seed/user/40/40.jpg" alt="Profile" class="w-full h-full object-cover">
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </nav>

    <!-- Category Filters -->
    <div class="bg-white border-b sticky top-16 z-30">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center space-x-2 py-3 overflow-x-auto scrollbar-hide">
                <button class="category-pill active px-4 py-1.5 rounded-full bg-gray-100 text-sm font-medium whitespace-nowrap" data-category="all">
                    All
                </button>
                <button class="category-pill px-4 py-1.5 rounded-full bg-gray-100 text-sm font-medium whitespace-nowrap hover:bg-gray-200" data-category="education">
                    Education
                </button>
                <button class="category-pill px-4 py-1.5 rounded-full bg-gray-100 text-sm font-medium whitespace-nowrap hover:bg-gray-200" data-category="music">
                    Music
                </button>
                <button class="category-pill px-4 py-1.5 rounded-full bg-gray-100 text-sm font-medium whitespace-nowrap hover:bg-gray-200" data-category="food">
                    Food
                </button>
                <button class="category-pill px-4 py-1.5 rounded-full bg-gray-100 text-sm font-medium whitespace-nowrap hover:bg-gray-200" data-category="travel">
                    Travel
                </button>
                <button class="category-pill px-4 py-1.5 rounded-full bg-gray-100 text-sm font-medium whitespace-nowrap hover:bg-gray-200" data-category="technology">
                    Technology
                </button>
            </div>
        </div>
    </div>

    <!-- Video Grid -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6">
        <div id="videoGrid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
            <!-- Videos will be dynamically inserted here -->
        </div>
    </main>

    <!-- Video Player Modal -->
    <div id="videoModal" class="fixed inset-0 z-50 hidden">
        <div class="modal-backdrop fixed inset-0" onclick="closeVideoModal()"></div>
        <div class="fixed inset-0 overflow-y-auto">
            <div class="flex items-center justify-center min-h-screen p-4">
                <div class="relative bg-white rounded-lg shadow-xl max-w-6xl w-full max-h-[90vh] overflow-hidden">
                    <!-- Modal Header -->
                    <div class="flex items-center justify-between p-4 border-b">
                        <h2 id="modalVideoTitle" class="text-xl font-semibold text-gray-900"></h2>
                        <button onclick="closeVideoModal()" class="p-2 rounded-full hover:bg-gray-100">
                            <i class="fas fa-times text-gray-600"></i>
                        </button>
                    </div>
                    
                    <!-- Video Player -->
                    <div class="relative bg-black">
                        <video id="videoPlayer" class="w-full" controls>
                            <source src="" type="video/mp4">
                            Your browser does not support the video tag.
                        </video>
                    </div>
                    
                    <!-- Video Info -->
                    <div class="p-6">
                        <div class="flex items-start justify-between mb-4">
                            <div>
                                <h3 id="modalVideoTitle2" class="text-lg font-semibold mb-2"></h3>
                                <div class="flex items-center space-x-4 text-sm text-gray-600">
                                    <span id="modalVideoViews"></span>
                                    <span id="modalVideoDate"></span>
                                </div>
                            </div>
                            <div class="flex items-center space-x-2">
                                <button class="flex items-center space-x-2 px-4 py-2 bg-gray-100 rounded-full hover:bg-gray-200">
                                    <i class="fas fa-thumbs-up"></i>
                                    <span>1.2K</span>
                                </button>
                                <button class="flex items-center space-x-2 px-4 py-2 bg-gray-100 rounded-full hover:bg-gray-200">
                                    <i class="fas fa-share"></i>
                                    <span>Share</span>
                                </button>
                            </div>
                        </div>
                        
                        <div class="border-t pt-4">
                            <p id="modalVideoDescription" class="text-gray-700 mb-4"></p>
                        </div>
                        
                        <!-- Comments Section -->
                        <div class="border-t pt-4">
                            <h4 class="font-semibold mb-4">Comments (24)</h4>
                            <div class="space-y-4 mb-4">
                                <div class="comment-item flex space-x-3">
                                    <img src="https://picsum.photos/seed/user1/40/40.jpg" alt="User" class="w-10 h-10 rounded-full">
                                    <div class="flex-1">
                                        <div class="bg-gray-100 rounded-lg p-3">
                                            <p class="font-medium text-sm">Sarah Johnson</p>
                                            <p class="text-sm text-gray-700">Great video! Very informative and well explained.</p>
                                        </div>
                                        <div class="flex items-center space-x-4 mt-1 text-xs text-gray-500">
                                            <button class="hover:text-gray-700">Like</button>
                                            <button class="hover:text-gray-700">Reply</button>
                                            <span>2 hours ago</span>
                                        </div>
                                    </div>
                                </div>
                                <div class="comment-item flex space-x-3">
                                    <img src="https://picsum.photos/seed/user2/40/40.jpg" alt="User" class="w-10 h-10 rounded-full">
                                    <div class="flex-1">
                                        <div class="bg-gray-100 rounded-lg p-3">
                                            <p class="font-medium text-sm">Mike Chen</p>
                                            <p class="text-sm text-gray-700">Thanks for sharing this! Really helped me understand the concept.</p>
                                        </div>
                                        <div class="flex items-center space-x-4 mt-1 text-xs text-gray-500">
                                            <button class="hover:text-gray-700">Like</button>
                                            <button class="hover:text-gray-700">Reply</button>
                                            <span>5 hours ago</span>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="flex space-x-3">
                                <img src="https://picsum.photos/seed/user/40/40.jpg" alt="User" class="w-10 h-10 rounded-full">
                                <div class="flex-1">
                                    <input type="text" placeholder="Add a comment..." class="w-full px-4 py-2 border border-gray-300 rounded-full focus:outline-none focus:border-blue-500">
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Upload Modal -->
    <div id="uploadModal" class="fixed inset-0 z-50 hidden">
        <div class="modal-backdrop fixed inset-0" onclick="closeUploadModal()"></div>
        <div class="fixed inset-0 overflow-y-auto">
            <div class="flex items-center justify-center min-h-screen p-4">
                <div class="relative bg-white rounded-lg shadow-xl max-w-2xl w-full">
                    <!-- Modal Header -->
                    <div class="flex items-center justify-between p-6 border-b">
                        <h2 class="text-2xl font-semibold text-gray-900">Upload Video</h2>
                        <button onclick="closeUploadModal()" class="p-2 rounded-full hover:bg-gray-100">
                            <i class="fas fa-times text-gray-600"></i>
                        </button>
                    </div>
                    
                    <!-- Upload Form -->
                    <div class="p-6">
                        <div class="upload-area p-12 rounded-lg text-center cursor-pointer mb-6" id="uploadArea">
                            <i class="fas fa-cloud-upload-alt text-6xl text-gray-400 mb-4"></i>
                            <p class="text-lg font-medium text-gray-700 mb-2">Drag and drop video files to upload</p>
                            <p class="text-sm text-gray-500 mb-4">or</p>
                            <button class="bg-blue-600 text-white px-6 py-2 rounded-full hover:bg-blue-700 transition-colors">
                                Browse Files
                            </button>
                            <input type="file" id="fileInput" class="hidden" accept="video/*">
                            <p class="text-xs text-gray-500 mt-4">Supported formats: MP4, MOV, AVI (Max 2GB)</p>
                        </div>
                        
                        <div class="space-y-4">
                            <div>
                                <label class="block text-sm font-medium text-gray-700 mb-2">Video Title</label>
                                <input type="text" placeholder="Enter video title" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:border-blue-500">
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700 mb-2">Description</label>
                                <textarea placeholder="Tell viewers about your video" rows="4" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:border-blue-500"></textarea>
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700 mb-2">Category</label>
                                <select class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:border-blue-500">
                                    <option>Select category</option>
                                    <option>Education</option>
                                    <option>Music</option>
                                    <option>Food</option>
                                    <option>Travel</option>
                                    <option>Technology</option>
                                </select>
                            </div>
                        </div>
                        
                        <div class="flex justify-end space-x-3 mt-6">
                            <button onclick="closeUploadModal()" class="px-6 py-2 border border-gray-300 rounded-lg hover:bg-gray-50">
                                Cancel
                            </button>
                            <button class="px-6 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700">
                                Upload Video
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Sample video data
        const videos = [
            {
                id: 1,
                title: "Learn JavaScript in 30 Minutes",
                author: "CodeMaster",
                views: "125K",
                duration: "30:45",
                thumbnail: "https://picsum.photos/seed/video1/320/180.jpg",
                category: "education",
                date: "2 days ago",
                description: "A comprehensive JavaScript tutorial covering the basics and advanced concepts. Perfect for beginners who want to learn programming quickly.",
                videoUrl: "https://www.w3schools.com/html/mov_bbb.mp4"
            },
            {
                id: 2,
                title: "Relaxing Piano Music - Study Session",
                author: "MusicFlow",
                views: "2.1M",
                duration: "45:20",
                thumbnail: "https://picsum.photos/seed/video2/320/180.jpg",
                category: "music",
                date: "1 week ago",
                description: "Beautiful piano music to help you focus while studying or working. This calming playlist features original compositions.",
                videoUrl: "https://www.w3schools.com/html/mov_bbb.mp4"
            },
            {
                id: 3,
                title: "Amazing Street Food Tour in Bangkok",
                author: "TravelWithMe",
                views: "890K",
                duration: "15:30",
                thumbnail: "https://picsum.photos/seed/video3/320/180.jpg",
                category: "travel",
                date: "3 days ago",
                description: "Join me on an incredible street food adventure through Bangkok! We'll try the best local dishes and explore hidden gems.",
                videoUrl: "https://www.w3schools.com/html/mov_bbb.mp4"
            },
            {
                id: 4,
                title: "Homemade Pizza Recipe - Perfect Crust",
                author: "ChefMaria",
                views: "345K",
                duration: "12:15",
                thumbnail: "https://picsum.photos/seed/video4/320/180.jpg",
                category: "food",
                date: "5 days ago",
                description: "Learn how to make the perfect homemade pizza with a crispy crust. This step-by-step recipe will make you look like a pro!",
                videoUrl: "https://www.w3schools.com/html/mov_bbb.mp4"
            },
            {
                id: 5,
                title: "AI and Machine Learning Explained",
                author: "TechInsights",
                views: "567K",
                duration: "22:40",
                thumbnail: "https://picsum.photos/seed/video5/320/180.jpg",
                category: "technology",
                date: "1 day ago",
                description: "Understand the fundamentals of AI and machine learning in this easy-to-follow tutorial. Perfect for tech enthusiasts!",
                videoUrl: "https://www.w3schools.com/html/mov_bbb.mp4"
            },
            {
                id: 6,
                title: "Morning Yoga Routine - 20 Minutes",
                author: "YogaLife",
                views: "1.2M",
                duration: "20:00",
                thumbnail: "https://picsum.photos/seed/video6/320/180.jpg",
                category: "education",
                date: "2 weeks ago",
                description: "Start your day with this energizing 20-minute yoga routine. Perfect for all levels, no experience needed!",
                videoUrl: "https://www.w3schools.com/html/mov_bbb.mp4"
            },
            {
                id: 7,
                title: "Electronic Music Production Basics",
                author: "BeatMaker",
                views: "234K",
                duration: "18:30",
                thumbnail: "https://picsum.photos/seed/video7/320/180.jpg",
                category: "music",
                date: "4 days ago",
                description: "Learn the basics of electronic music production using popular DAW software. Create your first track today!",
                videoUrl: "https://www.w3schools.com/html/mov_bbb.mp4"
            },
            {
                id: 8,
                title: "Tokyo Travel Guide 2024",
                author: "Wanderlust",
                views: "789K",
                duration: "25:45",
                thumbnail: "https://picsum.photos/seed/video8/320/180.jpg",
                category: "travel",
                date: "1 week ago",
                description: "Complete travel guide for Tokyo 2024. Best places to visit, eat, and stay in Japan's vibrant capital city.",
                videoUrl: "https://www.w3schools.com/html/mov_bbb.mp4"
            }
        ];

        let currentCategory = 'all';
        let filteredVideos = [...videos];

        // Initialize the app
        document.addEventListener('DOMContentLoaded', function() {
            renderVideos();
            setupEventListeners();
        });

        // Render videos
        function renderVideos() {
            const videoGrid = document.getElementById('videoGrid');
            videoGrid.innerHTML = '';
            
            filteredVideos.forEach(video => {
                const videoCard = createVideoCard(video);
                videoGrid.appendChild(videoCard);
            });
        }

        // Create video card element
        function createVideoCard(video) {
            const card = document.createElement('div');
            card.className = 'video-card cursor-pointer';
            card.onclick = () => openVideoModal(video);
            
            card.innerHTML = `
                <div class="relative rounded-lg overflow-hidden bg-white shadow-md hover:shadow-xl">
                    <div class="relative">
                        <img src="${video.thumbnail}" alt="${video.title}" class="w-full h-48 object-cover">
                        <span class="duration-badge absolute bottom-2 right-2 text-white text-xs px-2 py-1 rounded">
                            ${video.duration}
                        </span>
                    </div>
                    <div class="p-4">
                        <h3 class="font-semibold text-gray-900 mb-2 line-clamp-2">${video.title}</h3>
                        <p class="text-sm text-gray-600 mb-1">${video.author}</p>
                        <p class="text-xs text-gray-500">${video.views} views • ${video.date}</p>
                    </div>
                </div>
            `;
            
            return card;
        }

        // Setup event listeners
        function setupEventListeners() {
            // Category filters
            document.querySelectorAll('.category-pill').forEach(button => {
                button.addEventListener('click', function() {
                    document.querySelectorAll('.category-pill').forEach(btn => {
                        btn.classList.remove('active');
                    });
                    this.classList.add('active');
                    
                    currentCategory = this.dataset.category;
                    filterVideos();
                });
            });

            // Search functionality
            document.getElementById('searchInput').addEventListener('input', function(e) {
                const searchTerm = e.target.value.toLowerCase();
                filteredVideos = videos.filter(video => 
                    video.title.toLowerCase().includes(searchTerm) ||
                    video.author.toLowerCase().includes(searchTerm)
                );
                renderVideos();
            });

            // Upload button
            document.getElementById('uploadBtn').addEventListener('click', openUploadModal);

            // Upload area drag and drop
            const uploadArea = document.getElementById('uploadArea');
            const fileInput = document.getElementById('fileInput');

            uploadArea.addEventListener('click', () => fileInput.click());
            
            uploadArea.addEventListener('dragover', (e) => {
                e.preventDefault();
                uploadArea.classList.add('dragover');
            });
            
            uploadArea.addEventListener('dragleave', () => {
                uploadArea.classList.remove('dragover');
            });
            
            uploadArea.addEventListener('drop', (e) => {
                e.preventDefault();
                uploadArea.classList.remove('dragover');
                const files = e.dataTransfer.files;
                if (files.length > 0) {
                    handleFileUpload(files[0]);
                }
            });

            fileInput.addEventListener('change', (e) => {
                if (e.target.files.length > 0) {
                    handleFileUpload(e.target.files[0]);
                }
            });
        }

        // Filter videos by category
        function filterVideos() {
            if (currentCategory === 'all') {
                filteredVideos = [...videos];
            } else {
                filteredVideos = videos.filter(video => video.category === currentCategory);
            }
            renderVideos();
        }

        // Open video modal
        function openVideoModal(video) {
            const modal = document.getElementById('videoModal');
            const videoPlayer = document.getElementById('videoPlayer');
            
            document.getElementById('modalVideoTitle').textContent = video.title;
            document.getElementById('modalVideoTitle2').textContent = video.title;
            document.getElementById('modalVideoViews').textContent = `${video.views} views`;
            document.getElementById('modalVideoDate').textContent = video.date;
            document.getElementById('modalVideoDescription').textContent = video.description;
            
            videoPlayer.src = video.videoUrl;
            
            modal.classList.remove('hidden');
            document.body.style.overflow = 'hidden';
        }

        // Close video modal
        function closeVideoModal() {
            const modal = document.getElementById('videoModal');
            const videoPlayer = document.getElementById('videoPlayer');
            
            videoPlayer.pause();
            videoPlayer.currentTime = 0;
            
            modal.classList.add('hidden');
            document.body.style.overflow = 'auto';
        }

        // Open upload modal
        function openUploadModal() {
            document.getElementById('uploadModal').classList.remove('hidden');
            document.body.style.overflow = 'hidden';
        }

        // Close upload modal
        function closeUploadModal() {
            document.getElementById('uploadModal').classList.add('hidden');
            document.body.style.overflow = 'auto';
        }

        // Handle file upload (demo mode)
        function handleFileUpload(file) {
            if (file.type.startsWith('video/')) {
                // Simulate upload process
                const uploadArea = document.getElementById('uploadArea');
                uploadArea.innerHTML = `
                    <i class="fas fa-check-circle text-6xl text-green-500 mb-4"></i>
                    <p class="text-lg font-medium text-gray-700 mb-2">File selected: ${file.name}</p>
                    <p class="text-sm text-gray-500">Ready to upload!</p>
                `;
            } else {
                alert('Please select a valid video file.');
            }
        }

        // Close modals on escape key
        document.addEventListener('keydown', function(e) {
            if (e.key === 'Escape') {
                closeVideoModal();
                closeUploadModal();
            }
        });
    </script>
</body>
</html>

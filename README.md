<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Online Voting System</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom styles for Inter font and general aesthetics */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f2f5; /* Light grey background */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
            box-sizing: border-box;
        }
        .container {
            background-color: #ffffff;
            border-radius: 1.5rem; /* More rounded corners */
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
            padding: 2.5rem;
            width: 100%;
            max-width: 600px;
            text-align: center;
        }
        .section {
            display: none; /* Hidden by default */
        }
        .section.active {
            display: block; /* Shown when active */
        }
        .btn {
            @apply px-6 py-3 rounded-xl font-semibold text-white transition-all duration-300 ease-in-out shadow-md hover:shadow-lg focus:outline-none focus:ring-2 focus:ring-opacity-75;
        }
        .btn-primary {
            @apply bg-indigo-600 hover:bg-indigo-700 focus:ring-indigo-500;
        }
        .btn-secondary {
            @apply bg-gray-500 hover:bg-gray-600 focus:ring-gray-400;
        }
        .input-field {
            @apply w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-transparent;
        }
        .candidate-card {
            @apply flex items-center justify-between p-4 mb-3 bg-gray-50 rounded-xl border border-gray-200 cursor-pointer transition-all duration-200 hover:bg-indigo-50 hover:border-indigo-300;
        }
        .candidate-card.selected {
            @apply bg-indigo-100 border-indigo-500 ring-2 ring-indigo-500;
        }
        .message-box {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: white;
            border-radius: 1rem;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
            padding: 2rem;
            z-index: 1000;
            display: none;
            max-width: 400px;
            width: 90%;
            text-align: center;
        }
        .message-box-backdrop {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            z-index: 999;
            display: none;
        }
        video {
            width: 100%;
            max-width: 400px;
            height: auto;
            border-radius: 0.75rem;
            margin-top: 1rem;
            background-color: #000;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="text-4xl font-extrabold text-gray-800 mb-8">Online Voting System</h1>

        <!-- Message Box (Modal) -->
        <div id="messageBoxBackdrop" class="message-box-backdrop"></div>
        <div id="messageBox" class="message-box">
            <h3 id="messageBoxTitle" class="text-2xl font-bold mb-4 text-gray-900"></h3>
            <p id="messageBoxContent" class="text-gray-700 mb-6"></p>
            <button id="messageBoxCloseBtn" class="btn btn-primary">OK</button>
        </div>

        <!-- Login/Registration Section -->
        <div id="authSection" class="section active">
            <h2 class="text-3xl font-semibold text-gray-700 mb-6">Welcome!</h2>
            <div class="space-y-4 mb-6">
                <input type="text" id="usernameInput" placeholder="Enter Username" class="input-field">
                <input type="password" id="passwordInput" placeholder="Enter Password" class="input-field">
                <input type="text" id="biometricIdInput" placeholder="Enter Biometric ID (e.g., 12345)" class="input-field">
            </div>
            <div class="flex flex-col sm:flex-row justify-center space-y-4 sm:space-y-0 sm:space-x-4">
                <button id="registerBtn" class="btn btn-primary">Register</button>
                <button id="loginBtn" class="btn btn-secondary">Login</button>
            </div>
        </div>

        <!-- Biometric/Face Recognition Section -->
        <div id="verificationSection" class="section">
            <h2 class="text-3xl font-semibold text-gray-700 mb-6">Verify Your Identity</h2>
            <p class="text-gray-600 mb-4">
                <span class="font-bold text-red-500">Note:</span> Face recognition and biometric scan are simulated for demonstration purposes.
                In a real system, secure backend integration would be required.
            </p>

            <!-- Face Recognition Simulation -->
            <div class="mb-8 p-6 bg-blue-50 rounded-xl border border-blue-200">
                <h3 class="text-2xl font-medium text-blue-800 mb-4">Face Recognition</h3>
                <video id="webcamVideo" autoplay playsinline class="mb-4"></video>
                <button id="startWebcamBtn" class="btn btn-primary mb-2">Start Webcam</button>
                <button id="simulateFaceScanBtn" class="btn btn-secondary" disabled>Simulate Face Scan</button>
            </div>

            <!-- Biometric Scan Simulation -->
            <div class="mb-8 p-6 bg-green-50 rounded-xl border border-green-200">
                <h3 class="text-2xl font-medium text-green-800 mb-4">Biometric Scan (e.g., Fingerprint ID)</h3>
                <input type="text" id="biometricScanInput" placeholder="Enter your Biometric ID" class="input-field mb-4">
                <button id="simulateBiometricScanBtn" class="btn btn-primary">Simulate Biometric Scan</button>
            </div>

            <button id="proceedToVoteBtn" class="btn btn-primary mt-6" disabled>Proceed to Voting</button>
        </div>

        <!-- Voting Section -->
        <div id="votingSection" class="section">
            <h2 class="text-3xl font-semibold text-gray-700 mb-6">Cast Your Vote</h2>
            <div id="candidateList" class="mb-8 text-left">
                <!-- Candidates will be dynamically loaded here -->
            </div>
            <button id="castVoteBtn" class="btn btn-primary" disabled>Cast Vote</button>
        </div>

        <!-- Results Section -->
        <div id="resultsSection" class="section">
            <h2 class="text-3xl font-semibold text-gray-700 mb-6">Voting Results</h2>
            <div id="resultsDisplay" class="text-left">
                <!-- Results will be dynamically loaded here -->
            </div>
            <button id="logoutBtn" class="btn btn-secondary mt-6">Logout</button>
        </div>
    </div>

    <script>
        // Global variables for UI elements
        const authSection = document.getElementById('authSection');
        const verificationSection = document.getElementById('verificationSection');
        const votingSection = document.getElementById('votingSection');
        const resultsSection = document.getElementById('resultsSection');

        const usernameInput = document.getElementById('usernameInput');
        const passwordInput = document.getElementById('passwordInput');
        const biometricIdInput = document.getElementById('biometricIdInput');
        const registerBtn = document.getElementById('registerBtn');
        const loginBtn = document.getElementById('loginBtn');

        const webcamVideo = document.getElementById('webcamVideo');
        const startWebcamBtn = document.getElementById('startWebcamBtn');
        const simulateFaceScanBtn = document.getElementById('simulateFaceScanBtn');
        const biometricScanInput = document.getElementById('biometricScanInput');
        const simulateBiometricScanBtn = document.getElementById('simulateBiometricScanBtn');
        const proceedToVoteBtn = document.getElementById('proceedToVoteBtn');

        const candidateList = document.getElementById('candidateList');
        const castVoteBtn = document.getElementById('castVoteBtn');
        const resultsDisplay = document.getElementById('resultsDisplay');
        const logoutBtn = document.getElementById('logoutBtn');

        const messageBox = document.getElementById('messageBox');
        const messageBoxBackdrop = document.getElementById('messageBoxBackdrop');
        const messageBoxTitle = document.getElementById('messageBoxTitle');
        const messageBoxContent = document.getElementById('messageBoxContent');
        const messageBoxCloseBtn = document.getElementById('messageBoxCloseBtn');

        // In-memory data storage (simulated backend)
        let users = [];
        let currentUser = null;
        let candidates = [
            { id: 'candidateA', name: 'Alice Smith' },
            { id: 'candidateB', name: 'Bob Johnson' },
            { id: 'candidateC', name: 'Charlie Brown' }
        ];
        let votes = {}; // Stores vote counts: { candidateId: count }
        let hasVoted = {}; // Stores which user has voted: { userId: true/false }

        // State for verification
        let faceScanComplete = false;
        let biometricScanComplete = false;
        let webcamStream = null; // To store the webcam stream

        // --- Utility Functions ---

        /**
         * Displays a custom message box (modal) to the user.
         * @param {string} title - The title of the message box.
         * @param {string} message - The content message.
         */
        function showMessageBox(title, message) {
            messageBoxTitle.textContent = title;
            messageBoxContent.textContent = message;
            messageBox.style.display = 'block';
            messageBoxBackdrop.style.display = 'block';
        }

        /**
         * Hides the custom message box.
         */
        function hideMessageBox() {
            messageBox.style.display = 'none';
            messageBoxBackdrop.style.display = 'none';
        }

        /**
         * Switches the active section of the application.
         * @param {HTMLElement} sectionToShow - The section to make active.
         */
        function showSection(sectionToShow) {
            [authSection, verificationSection, votingSection, resultsSection].forEach(section => {
                section.classList.remove('active');
            });
            sectionToShow.classList.add('active');
        }

        /**
         * Updates the state of the 'Proceed to Voting' button based on verification status.
         */
        function updateProceedButtonState() {
            proceedToVoteBtn.disabled = !(faceScanComplete && biometricScanComplete);
        }

        // --- Authentication Logic ---

        /**
         * Handles user registration.
         */
        registerBtn.addEventListener('click', () => {
            const username = usernameInput.value.trim();
            const password = passwordInput.value.trim();
            const biometricId = biometricIdInput.value.trim();

            if (!username || !password || !biometricId) {
                showMessageBox('Error', 'Please fill in all registration fields.');
                return;
            }

            if (users.some(user => user.username === username)) {
                showMessageBox('Error', 'Username already exists. Please choose another.');
                return;
            }

            users.push({ username, password, biometricId, id: Date.now().toString() });
            showMessageBox('Success', 'Registration successful! You can now log in.');
            console.log('Registered Users:', users);
            // Clear fields after registration
            usernameInput.value = '';
            passwordInput.value = '';
            biometricIdInput.value = '';
        });

        /**
         * Handles user login.
         */
        loginBtn.addEventListener('click', () => {
            const username = usernameInput.value.trim();
            const password = passwordInput.value.trim();

            if (!username || !password) {
                showMessageBox('Error', 'Please enter your username and password.');
                return;
            }

            const user = users.find(u => u.username === username && u.password === password);

            if (user) {
                currentUser = user;
                showMessageBox('Success', `Welcome, ${currentUser.username}! Please verify your identity.`);
                showSection(verificationSection);
                // Reset verification state for new login
                faceScanComplete = false;
                biometricScanComplete = false;
                updateProceedButtonState();
                biometricScanInput.value = ''; // Clear biometric input
            } else {
                showMessageBox('Error', 'Invalid username or password.');
            }
        });

        // --- Verification Logic ---

        /**
         * Starts the webcam video stream.
         */
        startWebcamBtn.addEventListener('click', async () => {
            try {
                if (webcamStream) { // Stop existing stream if any
                    webcamStream.getTracks().forEach(track => track.stop());
                }
                const stream = await navigator.mediaDevices.getUserMedia({ video: true });
                webcamVideo.srcObject = stream;
                webcamStream = stream; // Store the stream
                simulateFaceScanBtn.disabled = false; // Enable face scan button
                showMessageBox('Webcam Active', 'Your webcam is now active. You can simulate a face scan.');
            } catch (err) {
                console.error('Error accessing webcam:', err);
                showMessageBox('Webcam Error', 'Could not access webcam. Please ensure you have a camera and have granted permission.');
                simulateFaceScanBtn.disabled = true;
            }
        });

        /**
         * Simulates a face scan.
         */
        simulateFaceScanBtn.addEventListener('click', () => {
            if (!webcamVideo.srcObject) {
                showMessageBox('Error', 'Please start the webcam first.');
                return;
            }
            faceScanComplete = true;
            showMessageBox('Face Scan', 'Face scan simulated successfully!');
            updateProceedButtonState();
        });

        /**
         * Simulates a biometric scan.
         */
        simulateBiometricScanBtn.addEventListener('click', () => {
            const enteredBiometricId = biometricScanInput.value.trim();
            if (!enteredBiometricId) {
                showMessageBox('Error', 'Please enter your Biometric ID.');
                return;
            }

            if (currentUser && currentUser.biometricId === enteredBiometricId) {
                biometricScanComplete = true;
                showMessageBox('Biometric Scan', 'Biometric scan simulated successfully!');
                updateProceedButtonState();
            } else {
                showMessageBox('Error', 'Invalid Biometric ID. Please try again.');
                biometricScanComplete = false;
                updateProceedButtonState();
            }
        });

        /**
         * Proceeds to the voting section after successful verification.
         */
        proceedToVoteBtn.addEventListener('click', () => {
            if (currentUser) {
                if (hasVoted[currentUser.id]) {
                    showMessageBox('Already Voted', 'You have already cast your vote.');
                    showSection(resultsSection); // Show results if already voted
                    renderResults();
                } else {
                    showSection(votingSection);
                    renderCandidates();
                }
            } else {
                showMessageBox('Error', 'No user logged in. Please log in first.');
                showSection(authSection);
            }
        });

        // --- Voting Logic ---

        let selectedCandidateId = null;

        /**
         * Renders the list of candidates for voting.
         */
        function renderCandidates() {
            candidateList.innerHTML = ''; // Clear previous list
            candidates.forEach(candidate => {
                const card = document.createElement('div');
                card.className = 'candidate-card';
                card.dataset.candidateId = candidate.id;
                card.innerHTML = `
                    <span class="text-lg font-medium text-gray-800">${candidate.name}</span>
                    <svg class="w-6 h-6 text-gray-400 check-icon" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
                    </svg>
                `;
                card.addEventListener('click', () => {
                    // Remove selected class from all cards
                    document.querySelectorAll('.candidate-card').forEach(c => c.classList.remove('selected'));
                    // Add selected class to the clicked card
                    card.classList.add('selected');
                    selectedCandidateId = candidate.id;
                    castVoteBtn.disabled = false; // Enable cast vote button
                });
                candidateList.appendChild(card);
            });
            castVoteBtn.disabled = true; // Disable until a candidate is selected
            selectedCandidateId = null; // Reset selection
        }

        /**
         * Handles casting a vote.
         */
        castVoteBtn.addEventListener('click', () => {
            if (!currentUser) {
                showMessageBox('Error', 'No user logged in. Please log in first.');
                showSection(authSection);
                return;
            }

            if (hasVoted[currentUser.id]) {
                showMessageBox('Already Voted', 'You have already cast your vote.');
                showSection(resultsSection);
                renderResults();
                return;
            }

            if (!selectedCandidateId) {
                showMessageBox('Error', 'Please select a candidate to vote for.');
                return;
            }

            // Record the vote
            votes[selectedCandidateId] = (votes[selectedCandidateId] || 0) + 1;
            hasVoted[currentUser.id] = true; // Mark user as voted

            showMessageBox('Vote Cast', `Your vote for ${candidates.find(c => c.id === selectedCandidateId).name} has been successfully cast!`);
            showSection(resultsSection);
            renderResults();
        });

        // --- Results Logic ---

        /**
         * Renders the current voting results.
         */
        function renderResults() {
            resultsDisplay.innerHTML = ''; // Clear previous results
            let totalVotes = 0;
            candidates.forEach(candidate => {
                const count = votes[candidate.id] || 0;
                totalVotes += count;
            });

            if (totalVotes === 0) {
                resultsDisplay.innerHTML = '<p class="text-gray-600">No votes cast yet.</p>';
                return;
            }

            candidates.forEach(candidate => {
                const count = votes[candidate.id] || 0;
                const percentage = totalVotes > 0 ? ((count / totalVotes) * 100).toFixed(2) : 0;

                const resultItem = document.createElement('div');
                resultItem.className = 'mb-4 p-4 bg-gray-50 rounded-xl border border-gray-200';
                resultItem.innerHTML = `
                    <h3 class="text-xl font-semibold text-gray-800">${candidate.name}</h3>
                    <p class="text-gray-700">Votes: ${count} (${percentage}%)</p>
                    <div class="w-full bg-gray-200 rounded-full h-2.5 mt-2">
                        <div class="bg-indigo-600 h-2.5 rounded-full" style="width: ${percentage}%"></div>
                    </div>
                `;
                resultsDisplay.appendChild(resultItem);
            });
            console.log('Current Votes:', votes);
        }

        /**
         * Handles user logout.
         */
        logoutBtn.addEventListener('click', () => {
            currentUser = null;
            selectedCandidateId = null;
            faceScanComplete = false;
            biometricScanComplete = false;
            if (webcamStream) {
                webcamStream.getTracks().forEach(track => track.stop());
                webcamVideo.srcObject = null;
                webcamStream = null;
            }
            simulateFaceScanBtn.disabled = true;
            proceedToVoteBtn.disabled = true;
            showMessageBox('Logged Out', 'You have been successfully logged out.');
            showSection(authSection);
            usernameInput.value = '';
            passwordInput.value = '';
            biometricIdInput.value = '';
            biometricScanInput.value = '';
        });

        // --- Initial Setup ---
        messageBoxCloseBtn.addEventListener('click', hideMessageBox);

        // Initialize vote counts to zero
        candidates.forEach(candidate => {
            votes[candidate.id] = 0;
        });

        // Ensure the auth section is shown on load
        showSection(authSection);
    </script>
</body>
</html>

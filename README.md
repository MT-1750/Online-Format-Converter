<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Online Format Converter</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/@ffmpeg/ffmpeg@0.9.7/dist/ffmpeg.min.js"></script>
    <!-- New libraries for PDF handling and zipping -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>
    <script>pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.worker.min.js';</script>

    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #d1fae5; /* A light green background */
            background-image: url("data:image/svg+xml,%3Csvg width='60' height='60' viewBox='0 0 0 0' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%239ca3af' fill-opacity='0.1'%3E%3Cpath d='M36 34v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zm0-30V0h-2v4h-4v2h4v4h2V6h4V4h-4zm0 18v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zM12 34v-4h-2v4H6v2h4v4h2v-4h4v-2h-4zm0-30V0h-2v4H6v2h4v4h2V6h4V4h-4zm0 18v-4h-2v4H6v2h4v4h2v-4h4v-2h-4zM48 34v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zm0-30V0h-2v4h-4v2h4v4h2V6h4V4h-4zm0 18v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zM24 60v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zm0-30v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zm0-30V0h-2v4h-4v2h4v4h2V6h4V4h-4z'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
            color: #1f2937;
        }
    </style>
</head>
<body class="p-4 sm:p-8 min-h-screen flex items-center justify-center flex-col">

    <div class="container mx-auto max-w-4xl p-6 bg-white rounded-2xl shadow-xl border border-gray-200 mb-8">
        <h1 class="text-3xl sm:text-4xl font-bold text-center text-gray-800 mb-2">Online Converter</h1>
        <p class="text-center text-gray-500 mb-8">Convert images, videos, audio, and documents on the fly.</p>

        <!-- Loading Indicator -->
        <div id="loading" class="hidden flex flex-col items-center justify-center p-6 bg-emerald-50 rounded-lg border border-emerald-200 mb-8">
            <div class="animate-spin rounded-full h-12 w-12 border-4 border-emerald-500 border-t-transparent"></div>
            <p class="mt-4 text-emerald-700 font-medium">Converting...</p>
        </div>

        <!-- Conversion Sections -->
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">

            <!-- Images Converter -->
            <div class="bg-gray-50 p-6 rounded-xl border border-gray-200 transition-all hover:shadow-lg">
                <h2 class="text-2xl font-semibold text-gray-700 mb-4 flex items-center">
                    <span class="mr-2">🖼️</span> Image Converter
                </h2>
                <p class="text-gray-500 text-sm mb-4">Select one or more images to convert.</p>
                <input type="file" id="imageInput" multiple accept="image/*" class="w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-emerald-50 file:text-emerald-700 hover:file:bg-emerald-100 mb-4">
                <div class="flex flex-col sm:flex-row items-center space-y-4 sm:space-y-0 sm:space-x-4 mb-4">
                    <label for="imageFormat" class="text-gray-600 font-medium whitespace-nowrap">Convert to:</label>
                    <select id="imageFormat" class="w-full px-3 py-2 border border-gray-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-transparent">
                        <option value="png">PNG</option>
                        <option value="jpg">JPG</option>
                        <option value="webp">WEBP</option>
                        <option value="pdf">PDF</option>
                    </select>
                </div>
                <button id="convertImageBtn" class="w-full bg-emerald-600 text-white py-3 px-6 rounded-lg font-bold shadow-md hover:bg-emerald-700 transition-colors disabled:bg-gray-400">Convert Image</button>
                <div id="imageDownloadContainer" class="hidden flex-col items-center mt-4 w-full">
                    <p class="text-green-700 font-semibold mb-2">Download Options:</p>
                    <div class="flex flex-wrap justify-center space-x-2 space-y-2">
                        <button id="downloadImagesZipBtn" class="bg-blue-600 text-white py-2 px-4 rounded-lg font-bold shadow-md hover:bg-blue-700 transition-colors">Download All as ZIP</button>
                    </div>
                    <div id="imageDownloadLinks" class="flex flex-col space-y-2 mt-4"></div>
                </div>
            </div>

            <!-- Videos Converter -->
            <div class="bg-gray-50 p-6 rounded-xl border border-gray-200 transition-all hover:shadow-lg">
                <h2 class="text-2xl font-semibold text-gray-700 mb-4 flex items-center">
                    <span class="mr-2">🎥</span> Video Converter
                </h2>
                <input type="file" id="videoInput" accept="video/*" class="w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-emerald-50 file:text-emerald-700 hover:file:bg-emerald-100 mb-4">
                <div class="flex flex-col sm:flex-row items-center space-y-4 sm:space-y-0 sm:space-x-4 mb-4">
                    <label for="videoFormat" class="text-gray-600 font-medium whitespace-nowrap">Convert to:</label>
                    <select id="videoFormat" class="w-full px-3 py-2 border border-gray-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-transparent">
                        <option value="mp4">MP4</option>
                        <option value="webm">WEBM</option>
                        <option value="avi">AVI</option>
                    </select>
                </div>
                <button id="convertVideoBtn" class="w-full bg-emerald-600 text-white py-3 px-6 rounded-lg font-bold shadow-md hover:bg-emerald-700 transition-colors disabled:bg-gray-400">Convert Video</button>
                <a id="videoDownloadLink" class="hidden text-center mt-4 w-full bg-green-500 text-white py-3 px-6 rounded-lg font-bold shadow-md hover:bg-green-600 transition-colors" href="#" download>Download Converted Video</a>
            </div>

            <!-- Audio Converter -->
            <div class="bg-gray-50 p-6 rounded-xl border border-gray-200 transition-all hover:shadow-lg">
                <h2 class="text-2xl font-semibold text-gray-700 mb-4 flex items-center">
                    <span class="mr-2">🎵</span> Audio Converter
                </h2>
                <input type="file" id="audioInput" accept="audio/*" class="w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-emerald-50 file:text-emerald-700 hover:file:bg-emerald-100 mb-4">
                <div class="flex flex-col sm:flex-row items-center space-y-4 sm:space-y-0 sm:space-x-4 mb-4">
                    <label for="audioFormat" class="text-gray-600 font-medium whitespace-nowrap">Convert to:</label>
                    <select id="audioFormat" class="w-full px-3 py-2 border border-gray-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-transparent">
                        <option value="mp3">MP3</option>
                        <option value="wav">WAV</option>
                        <option value="ogg">OGG</option>
                    </select>
                </div>
                <button id="convertAudioBtn" class="w-full bg-emerald-600 text-white py-3 px-6 rounded-lg font-bold shadow-md hover:bg-emerald-700 transition-colors disabled:bg-gray-400">Convert Audio</button>
                <a id="audioDownloadLink" class="hidden text-center mt-4 w-full bg-green-500 text-white py-3 px-6 rounded-lg font-bold shadow-md hover:bg-green-600 transition-colors" href="#" download>Download Converted Audio</a>
            </div>

            <!-- Documents Converter -->
            <div class="bg-gray-50 p-6 rounded-xl border border-gray-200 transition-all hover:shadow-lg">
                <h2 class="text-2xl font-semibold text-gray-700 mb-4 flex items-center">
                    <span class="mr-2">📄</span> Document Converter
                </h2>
                <input type="file" id="documentInput" accept=".txt,.html" class="w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-emerald-50 file:text-emerald-700 hover:file:bg-emerald-100 mb-4">
                <div class="flex flex-col sm:flex-row items-center space-y-4 sm:space-y-0 sm:space-x-4 mb-4">
                    <label for="documentFormat" class="text-gray-600 font-medium whitespace-nowrap">Convert to:</label>
                    <select id="documentFormat" class="w-full px-3 py-2 border border-gray-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-transparent">
                        <option value="txt">TXT</option>
                        <option value="html">HTML</option>
                    </select>
                </div>
                <button id="convertDocumentBtn" class="w-full bg-emerald-600 text-white py-3 px-6 rounded-lg font-bold shadow-md hover:bg-emerald-700 transition-colors disabled:bg-gray-400">Convert Document</button>
                <a id="documentDownloadLink" class="hidden text-center mt-4 w-full bg-green-500 text-white py-3 px-6 rounded-lg font-bold shadow-md hover:bg-green-600 transition-colors" href="#" download>Download Converted Document</a>
            </div>
            
            <!-- PDF to Images Converter Section with new download options -->
            <div class="bg-gray-50 p-6 rounded-xl border border-gray-200 transition-all hover:shadow-lg lg:col-span-2">
                <h2 class="text-2xl font-semibold text-gray-700 mb-4 flex items-center">
                    <span class="mr-2">📄</span> PDF to Images Converter
                </h2>
                <p class="text-gray-500 text-sm mb-4">Select one PDF to convert its pages into images.</p>
                <input type="file" id="pdfInput" accept=".pdf" class="w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-emerald-50 file:text-emerald-700 hover:file:bg-emerald-100 mb-4">
                <div class="flex flex-col sm:flex-row items-center space-y-4 sm:space-y-0 sm:space-x-4 mb-4">
                    <label for="pdfToImageFormat" class="text-gray-600 font-medium whitespace-nowrap">Convert to:</label>
                    <select id="pdfToImageFormat" class="w-full px-3 py-2 border border-gray-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-transparent">
                        <option value="png">PNG</option>
                        <option value="jpg">JPG</option>
                    </select>
                </div>
                <button id="convertPdfToImageBtn" class="w-full bg-emerald-600 text-white py-3 px-6 rounded-lg font-bold shadow-md hover:bg-emerald-700 transition-colors disabled:bg-gray-400">Convert PDF</button>
                <div id="pdfDownloadContainer" class="hidden flex-col items-center mt-4 w-full">
                    <p class="text-green-700 font-semibold mb-2">Download Options:</p>
                    <div class="flex flex-wrap justify-center space-x-2 space-y-2">
                        <button id="downloadAllZipBtn" class="bg-blue-600 text-white py-2 px-4 rounded-lg font-bold shadow-md hover:bg-blue-700 transition-colors">Download All as ZIP</button>
                    </div>
                    <div id="pdfDownloadLinks" class="flex flex-col space-y-2 mt-4"></div>
                </div>
            </div>

        </div>

        <!-- Custom Modal/Message Box -->
        <div id="modal" class="fixed inset-0 z-50 hidden items-center justify-center bg-gray-900 bg-opacity-50 transition-opacity duration-300">
            <div class="bg-white p-6 rounded-xl shadow-2xl max-w-sm w-full mx-4">
                <div class="flex justify-between items-center mb-4">
                    <h3 id="modalTitle" class="text-xl font-bold text-gray-800">Message</h3>
                    <button id="closeModalBtn" class="text-gray-500 hover:text-gray-700">&times;</button>
                </div>
                <p id="modalMessage" class="text-gray-600"></p>
                <div class="flex justify-end mt-4">
                    <button id="okModalBtn" class="bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700 transition-colors">OK</button>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Footer -->
    <footer class="mt-8 py-4 w-full text-center text-gray-600 text-sm">
        <p>Built with ❤️</p>
        <p>&copy; 2025 Online Converter. All rights reserved.</p>
    </footer>

    <script>
        // Initialize FFmpeg library
        const { createFFmpeg, fetchFile } = FFmpeg;
        const ffmpeg = createFFmpeg({ log: true });

        // Utility function to show a modal with a message
        const showModal = (title, message) => {
            document.getElementById('modalTitle').textContent = title;
            document.getElementById('modalMessage').textContent = message;
            document.getElementById('modal').classList.remove('hidden');
            document.getElementById('modal').classList.add('flex');
        };

        // Utility function to hide the modal
        const hideModal = () => {
            document.getElementById('modal').classList.add('hidden');
            document.getElementById('modal').classList.remove('flex');
        };
        
        document.getElementById('closeModalBtn').addEventListener('click', hideModal);
        document.getElementById('okModalBtn').addEventListener('click', hideModal);

        // Helper function to show and hide the loading indicator
        const showLoading = () => {
            document.getElementById('loading').classList.remove('hidden');
            document.getElementById('loading').classList.add('flex');
        };
        const hideLoading = () => {
            document.getElementById('loading').classList.add('hidden');
            document.getElementById('loading').classList.remove('flex');
        };
        
        // Helper function to extract filename without extension
        const getFileNameWithoutExtension = (fileName) => {
            const lastDotIndex = fileName.lastIndexOf('.');
            if (lastDotIndex === -1) {
                return fileName;
            }
            return fileName.substring(0, lastDotIndex);
        };


        // Stores converted files for zipping
        let convertedPdfImages = [];
        let convertedImageBlobs = [];

        // Conversion functions
        const handleImageConversion = (files) => {
            return new Promise(async (resolve, reject) => {
                const format = document.getElementById('imageFormat').value;

                if (format === 'pdf') {
                    // Image(s) to PDF conversion
                    const { jsPDF } = window.jspdf;
                    const doc = new jsPDF({
                        orientation: 'portrait',
                        unit: 'pt',
                        format: 'a4'
                    });

                    for (let i = 0; i < files.length; i++) {
                        const file = files[i];
                        const imgData = await new Promise(resolve => {
                            const reader = new FileReader();
                            reader.onload = (e) => resolve(e.target.result);
                            reader.readAsDataURL(file);
                        });

                        const img = new Image();
                        await new Promise(resolve => {
                            img.onload = resolve;
                            img.src = imgData;
                        });
                        
                        const imgWidth = img.width;
                        const imgHeight = img.height;

                        const pdfWidth = doc.internal.pageSize.getWidth();
                        const pdfHeight = doc.internal.pageSize.getHeight();
                        
                        let ratio = Math.min(pdfWidth / imgWidth, pdfHeight / imgHeight);
                        const scaledWidth = imgWidth * ratio;
                        const scaledHeight = imgHeight * ratio;

                        const x = (pdfWidth - scaledWidth) / 2;
                        const y = (pdfHeight - scaledHeight) / 2;

                        if (i > 0) doc.addPage();
                        doc.addImage(imgData, 'JPEG', x, y, scaledWidth, scaledHeight);
                    }

                    const pdfBlob = doc.output('blob');
                    resolve([{ blob: pdfBlob, format: 'pdf', fileName: 'converted.pdf' }]);
                    
                } else {
                    // Image to Image conversion
                    const convertedFiles = [];
                    for (const file of files) {
                        const reader = new FileReader();
                        const blob = await new Promise((resolveReader, rejectReader) => {
                            reader.onload = (e) => {
                                const img = new Image();
                                img.onload = () => {
                                    const canvas = document.createElement('canvas');
                                    canvas.width = img.width;
                                    canvas.height = img.height;
                                    const ctx = canvas.getContext('2d');
                                    ctx.drawImage(img, 0, 0);

                                    let mimeType = `image/${format}`;
                                    if (format === 'jpg') mimeType = 'image/jpeg';
                                    
                                    canvas.toBlob((blob) => {
                                        if (blob) {
                                            resolveReader(blob);
                                        } else {
                                            rejectReader('Failed to convert image to Blob.');
                                        }
                                    }, mimeType);
                                };
                                img.src = e.target.result;
                            };
                            reader.onerror = () => rejectReader('Error reading file.');
                            reader.readAsDataURL(file);
                        });
                        convertedFiles.push({ blob, format, fileName: file.name });
                    }
                    resolve(convertedFiles);
                }
            });
        };

        const handleVideoAudioConversion = async (file, format) => {
            showLoading();
            if (!ffmpeg.isLoaded()) {
                await ffmpeg.load();
            }
            ffmpeg.FS('writeFile', file.name, await fetchFile(file));
            const outputName = `output.${format}`;
            
            let args = ['-i', file.name, outputName];
            
            // For audio conversions, specifically set the audio codec
            if (file.type.startsWith('audio/')) {
                args = ['-i', file.name, '-acodec', format, outputName];
            } else if (file.type.startsWith('video/')) {
                 // For video, add a video codec argument to ensure compatibility
                args = ['-i', file.name, '-vcodec', 'libx264', outputName];
                if(format === 'webm'){
                    args = ['-i', file.name, '-vcodec', 'libvpx-vp9', outputName];
                }
            }
            
            await ffmpeg.run(...args);
            const data = ffmpeg.FS('readFile', outputName);
            
            const blob = new Blob([data.buffer], { type: `${file.type.split('/')[0]}/${format}` });
            hideLoading();
            return { blob, format };
        };

        const handleDocumentConversion = (file) => {
            return new Promise((resolve, reject) => {
                const reader = new FileReader();
                reader.onload = (e) => {
                    const textContent = e.target.result;
                    const format = document.getElementById('documentFormat').value;
                    let newContent;
                    let newMimeType;
                    
                    if (format === 'txt') {
                        // Simple conversion from anything to text
                        newContent = textContent;
                        newMimeType = 'text/plain';
                    } else if (format === 'html') {
                        // Simple conversion from text to HTML
                        newContent = `<!DOCTYPE html><html><body><pre>${textContent}</pre></body></html>`;
                        newMimeType = 'text/html';
                    } else {
                        reject('Unsupported document format.');
                        return;
                    }
                    
                    const blob = new Blob([newContent], { type: newMimeType });
                    resolve({ blob, format });
                };
                reader.onerror = () => reject('Error reading document file.');
                reader.readAsText(file);
            });
        };

        // PDF to Images Conversion logic
        const handlePdfToImageConversion = async (file, format) => {
            showLoading();
            convertedPdfImages = []; // Clear previous results
            const fileArrayBuffer = await file.arrayBuffer();
            const pdf = await pdfjsLib.getDocument({ data: fileArrayBuffer }).promise;
            const numPages = pdf.numPages;
            const downloadLinksContainer = document.getElementById('pdfDownloadLinks');
            downloadLinksContainer.innerHTML = '';
            
            const baseFileName = getFileNameWithoutExtension(file.name);

            for (let i = 1; i <= numPages; i++) {
                const page = await pdf.getPage(i);
                const viewport = page.getViewport({ scale: 1.5 });
                const canvas = document.createElement('canvas');
                const canvasContext = canvas.getContext('2d');
                canvas.height = viewport.height;
                canvas.width = viewport.width;

                await page.render({ canvasContext, viewport }).promise;

                const mimeType = format === 'jpg' ? 'image/jpeg' : `image/${format}`;
                const blob = await new Promise(resolve => canvas.toBlob(resolve, mimeType));
                
                // Store the blob and filename for later use (zipping)
                convertedPdfImages.push({
                    blob,
                    fileName: `${baseFileName}-page-${i}.${format}`
                });

                // Create individual download link
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `${baseFileName}-page-${i}.${format}`;
                a.textContent = `Download Page ${i} .${format}`;
                a.className = "text-center w-full bg-green-500 text-white py-2 px-4 rounded-lg font-bold shadow-md hover:bg-green-600 transition-colors";
                downloadLinksContainer.appendChild(a);
            }
            document.getElementById('pdfDownloadContainer').classList.remove('hidden');
            hideLoading();
        };

        // NEW logic to zip and download all converted images
        const handleZipDownload = async (filesToZip, zipFileName) => {
            if (filesToZip.length === 0) {
                showModal("Error", "No files to download. Please perform a conversion first.");
                return;
            }
            
            showLoading();
            const zip = new JSZip();
            
            // Add each converted file to the zip file
            filesToZip.forEach(file => {
                zip.file(file.fileName, file.blob);
            });
            
            try {
                // Generate the zip file and trigger download
                const zipBlob = await zip.generateAsync({ type: "blob" });
                const url = URL.createObjectURL(zipBlob);
                const a = document.createElement('a');
                a.href = url;
                a.download = zipFileName;
                a.click();
                URL.revokeObjectURL(url); // Clean up the URL object
            } catch (error) {
                showModal("Zipping Error", "Failed to create zip file. Please try again.");
                console.error("Zipping error:", error);
            } finally {
                hideLoading();
            }
        };


        // Event listeners
        document.getElementById('convertImageBtn').addEventListener('click', async () => {
            const fileInput = document.getElementById('imageInput');
            if (!fileInput.files.length) {
                showModal("Error", "Please select an image file first.");
                return;
            }
            showLoading();
            const files = fileInput.files;
            convertedImageBlobs = []; // Clear previous results
            const downloadLinksContainer = document.getElementById('imageDownloadLinks');
            downloadLinksContainer.innerHTML = '';
            
            try {
                const results = await handleImageConversion(files);
                
                results.forEach(result => {
                    // Store the blob for zipping
                    convertedImageBlobs.push(result);
                    
                    const { blob, format, fileName } = result;
                    const url = URL.createObjectURL(blob);
                    
                    const a = document.createElement('a');
                    a.href = url;
                    a.download = `${getFileNameWithoutExtension(fileName)}.${format}`;
                    a.textContent = `Download converted .${format}`;
                    a.className = "text-center w-full bg-green-500 text-white py-2 px-4 rounded-lg font-bold shadow-md hover:bg-green-600 transition-colors";
                    downloadLinksContainer.appendChild(a);
                });
                
                document.getElementById('imageDownloadContainer').classList.remove('hidden');
                hideLoading();
            } catch (error) {
                hideLoading();
                showModal("Conversion Error", error.message || error);
            }
        });

        document.getElementById('convertVideoBtn').addEventListener('click', async () => {
            const fileInput = document.getElementById('videoInput');
            if (!fileInput.files.length) {
                showModal("Error", "Please select a video file first.");
                return;
            }
            const file = fileInput.files[0];
            const format = document.getElementById('videoFormat').value;
            const downloadLink = document.getElementById('videoDownloadLink');

            try {
                const { blob } = await handleVideoAudioConversion(file, format);
                const fileName = getFileNameWithoutExtension(file.name);
                const url = URL.createObjectURL(blob);
                downloadLink.href = url;
                downloadLink.download = `${fileName}.${format}`;
                downloadLink.textContent = `Download converted .${format}`;
                downloadLink.classList.remove('hidden');
            } catch (error) {
                hideLoading();
                showModal("Conversion Error", error.message || "Failed to convert video. Please try a different format or a smaller file.");
            }
        });
        
        document.getElementById('convertAudioBtn').addEventListener('click', async () => {
            const fileInput = document.getElementById('audioInput');
            if (!fileInput.files.length) {
                showModal("Error", "Please select an audio file first.");
                return;
            }
            const file = fileInput.files[0];
            const format = document.getElementById('audioFormat').value;
            const downloadLink = document.getElementById('audioDownloadLink');

            try {
                const { blob } = await handleVideoAudioConversion(file, format);
                const fileName = getFileNameWithoutExtension(file.name);
                const url = URL.createObjectURL(blob);
                downloadLink.href = url;
                downloadLink.download = `${fileName}.${format}`;
                downloadLink.textContent = `Download converted .${format}`;
                downloadLink.classList.remove('hidden');
            } catch (error) {
                hideLoading();
                showModal("Conversion Error", error.message || "Failed to convert audio. Please try a different format or a smaller file.");
            }
        });

        document.getElementById('convertDocumentBtn').addEventListener('click', async () => {
            const fileInput = document.getElementById('documentInput');
            if (!fileInput.files.length) {
                showModal("Error", "Please select a document file first.");
                return;
            }
            showLoading();
            const file = fileInput.files[0];
            const downloadLink = document.getElementById('documentDownloadLink');
            
            try {
                const { blob, format } = await handleDocumentConversion(file);
                const fileName = getFileNameWithoutExtension(file.name);
                const url = URL.createObjectURL(blob);
                downloadLink.href = url;
                downloadLink.download = `${fileName}.${format}`;
                downloadLink.textContent = `Download converted .${format}`;
                downloadLink.classList.remove('hidden');
                hideLoading();
            } catch (error) {
                hideLoading();
                showModal("Conversion Error", error.message || error);
            }
        });
        
        // Event listeners for PDF conversion
        document.getElementById('convertPdfToImageBtn').addEventListener('click', async () => {
            const fileInput = document.getElementById('pdfInput');
            if (!fileInput.files.length) {
                showModal("Error", "Please select a PDF file first.");
                return;
            }
            const file = fileInput.files[0];
            const format = document.getElementById('pdfToImageFormat').value;
            
            try {
                await handlePdfToImageConversion(file, format);
            } catch (error) {
                hideLoading();
                showModal("Conversion Error", error.message || "An error occurred during conversion.");
            }
        });

        document.getElementById('downloadAllZipBtn').addEventListener('click', () => {
            handleZipDownload(convertedPdfImages, 'converted-pdf-images.zip');
        });
        
        document.getElementById('downloadImagesZipBtn').addEventListener('click', () => {
            handleZipDownload(convertedImageBlobs, 'converted-images.zip');
        });
    </script>
</body>
</html>

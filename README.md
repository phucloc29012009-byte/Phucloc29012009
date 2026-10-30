<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Điện Ảnh Việt - Trại Phim Việt Nam</title>
    <style>
        /* CSS GIAO DIỆN NỀN TỐI (DARK MODE) */
        :root {
            --bg-color: #111;
            --card-bg: #222;
            --text-color: #fff;
            --accent-color: #e50914; /* Màu đỏ Netflix */
            --text-muted: #aaa;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            padding-bottom: 50px;
        }

        /* Header & Thanh tìm kiếm */
        header {
            background-color: #000;
            padding: 20px;
            text-align: center;
            position: sticky;
            top: 0;
            z-index: 100;
            box-shadow: 0 4px 10px rgba(0,0,0,0.5);
        }

        header h1 {
            color: var(--accent-color);
            margin-bottom: 15px;
            font-size: 2rem;
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .controls {
            display: flex;
            justify-content: center;
            gap: 10px;
            flex-wrap: wrap;
        }

        input[type="text"], select {
            padding: 10px 15px;
            border: none;
            border-radius: 5px;
            background-color: #333;
            color: #fff;
            font-size: 1rem;
            outline: none;
        }

        input[type="text"] {
            width: 300px;
        }

        /* Danh sách phim */
        .container {
            max-width: 1200px;
            margin: 30px auto;
            padding: 0 20px;
        }

        .movie-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
            gap: 30px;
        }

        .movie-card {
            background-color: var(--card-bg);
            border-radius: 8px;
            overflow: hidden;
            cursor: pointer;
            transition: transform 0.3s ease, box-shadow 0.3s ease;
            box-shadow: 0 4px 6px rgba(0,0,0,0.3);
        }

        .movie-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 15px rgba(229, 9, 20, 0.3);
        }

        .movie-poster {
            width: 100%;
            height: 320px;
            object-fit: cover;
            background-color: #333;
        }

        .movie-info {
            padding: 15px;
        }

        .movie-title {
            font-size: 1.1rem;
            margin-bottom: 8px;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .movie-meta {
            display: flex;
            justify-content: space-between;
            font-size: 0.85rem;
            color: var(--text-muted);
        }

        .rating {
            color: #f39c12;
            font-weight: bold;
        }

        /* Cửa sổ Modal chi tiết phim */
        .modal {
            display: none;
            position: fixed;
            top: 0; left: 0;
            width: 100%; height: 100%;
            background-color: rgba(0,0,0,0.85);
            z-index: 1000;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .modal-content {
            background-color: #181818;
            max-width: 700px;
            width: 100%;
            border-radius: 10px;
            overflow: hidden;
            position: relative;
            animation: fadeIn 0.4s ease;
            display: flex;
            flex-direction: column;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: scale(0.9); }
            to { opacity: 1; transform: scale(1); }
        }

        .close-btn {
            position: absolute;
            top: 15px; right: 20px;
            font-size: 2rem;
            color: #fff;
            cursor: pointer;
            z-index: 10;
        }

        .modal-body {
            display: flex;
            padding: 25px;
            gap: 20px;
        }

        @media (max-width: 600px) {
            .modal-body { flex-direction: column; }
            input[type="text"] { width: 100%; }
        }

        .modal-poster {
            width: 200px;
            height: 300px;
            object-fit: cover;
            border-radius: 5px;
            flex-shrink: 0;
        }

        .modal-details h2 {
            margin-bottom: 10px;
            color: var(--accent-color);
        }

        .modal-details p {
            margin-bottom: 10px;
            line-height: 1.6;
        }

        .modal-details .label {
            font-weight: bold;
            color: var(--text-muted);
        }
    </style>
</head>
<body>

    <!-- HEADER & TÌM KIẾM -->
    <header>
        <h1>Điện Ảnh Việt</h1>
        <div class="controls">
            <input type="text" id="searchBar" placeholder="Tìm tên phim Việt Nam...">
            <select id="genreFilter">
                <option value="all">Tất cả thể loại</option>
                <option value="Hành động">Hành động</option>
                <option value="Hài">Hài</option>
                <option value="Tâm lý">Tâm lý</option>
                <option value="Kinh dị">Kinh dị</option>
            </select>
        </div>
    </header>

    <!-- DANH SÁCH PHIM -->
    <div class="container">
        <div class="movie-grid" id="movieGrid">
            <!-- Dữ liệu phim sẽ tự động render ở đây bằng Javascript -->
        </div>
    </div>

    <!-- MODAL XEM CHI TIẾT -->
    <div class="modal" id="movieModal">
        <div class="modal-content">
            <span class="close-btn" id="closeModal">&times;</span>
            <div class="modal-body">
                <img src="" alt="" class="modal-poster" id="modalPoster">
                <div class="modal-details">
                    <h2 id="modalTitle">Tên Phim</h2>
                    <p><span class="label">Năm phát hành:</span> <span id="modalYear">2024</span></p>
                    <p><span class="label">Thể loại:</span> <span id="modalGenre">Hành động</span></p>
                    <p><span class="label">Đánh giá:</span> <span id="modalRating" class="rating">8.5</span>/10</p>
                    <p><span class="label">Nội dung:</span> <span id="modalDesc">Mô tả phim...</span></p>
                </div>
            </div>
        </div>
    </div>

    <!-- JAVASCRIPT XỬ LÝ DỮ LIỆU -->
    <script>
        // 1. Kho dữ liệu phim mẫu (Bạn có thể thêm phim tùy ý vào đây)
        const movies = [
            {
                title: "Mai",
                year: "2024",
                genre: "Tâm lý",
                rating: "8.2",
                poster: "https://unsplash.com", // Thay link ảnh thật vào đây
                desc: "Câu chuyện tình cảm đầy trắc trở giữa Mai - một người phụ nữ làm nghề mát-xa và Dương - một chàng trai nhạc công trẻ tuổi đào hoa."
            },
            {
                title: "Lật Mặt 7: Một Điều Ước",
                year: "2024",
                genre: "Tâm lý",
                rating: "8.7",
                poster: "https://unsplash.com",
                desc: "Bộ phim cảm động về tình mẫu tử của bà Hai và 5 người con khi trưởng thành mỗi người một phương, ai cũng có nỗi khổ riêng."
            },
            {
                title: "Đất Rừng Phương Nam",
                year: "2023",
                genre: "Hành động",
                rating: "7.5",
                poster: "https://unsplash.com",
                desc: "Hành trình tìm cha đầy gian nan nhưng kiêu hùng của chú bé An dọc khắp lục tỉnh Nam Kỳ kháng Pháp đầu thế kỷ 20."
            },
            {
                title: "Nhà Bà Nữ",
                year: "2023",
                genre: "Hài",
                rating: "7.9",
                poster: "https://unsplash.com",
                desc: "Câu chuyện gia đình ba thế hệ bán bánh canh cua của bà Nữ với những mâu thuẫn thế hệ gay gắt và bài học sâu sắc."
            },
            {
                title: "Kẻ Ăn Hồn",
                year: "2023",
                genre: "Kinh dị",
                rating: "7.2",
                poster: "https://unsplash.com",
                desc: "Phim kinh dị cổ trang dựa trên câu chuyện về ngôi làng Địa Ngục cùng loại cổ thuật Rượu sọ người bí ẩn."
            }
        ];

        const movieGrid = document.getElementById('movieGrid');
        const searchBar = document.getElementById('searchBar');
        const genreFilter = document.getElementById('genreFilter');
        
        const modal = document.getElementById('movieModal');
        const closeModal = document.getElementById('closeModal');

        // 2. Hàm hiển thị danh sách phim lên màn hình
        function displayMovies(filteredMovies) {
            movieGrid.innerHTML = '';
            if (filteredMovies.length === 0) {
                movieGrid.innerHTML = `<p style="grid-column: 1/-1; text-align:center; color: var(--text-muted);">Không tìm thấy phim phù hợp.</p>`;
                return;
            }

            filteredMovies.forEach(movie => {
                const card = document.createElement('div');
                card.classList.add('movie-card');
                card.innerHTML = `
                    <img src="${movie.poster}" alt="${movie.title}" class="movie-poster">
                    <div class="movie-info">
                        <h3 class="movie-title">${movie.title}</h3>

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Rule34 Post Viewer</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
  <style>
    .post {
      margin: 15px 0;
    }
    .post img {
      max-width: 100%;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1 class="mt-5">Rule34 Post Viewer</h1>
    <form id="searchForm" class="my-3">
      <div class="input-group">
        <input type="text" id="tags" class="form-control" placeholder="Enter tags (e.g., anime, character)">
        <button type="submit" class="btn btn-primary">Search</button>
      </div>
    </form>
    <div id="postContainer" class="row"></div>
    <nav id="pagination" class="mt-4">
      <ul class="pagination justify-content-center"></ul>
    </nav>
  </div>

  <script>
    const apiBase = "https://api.rule34.xxx/index.php?page=dapi&s=post&q=index";
    const postContainer = document.getElementById("postContainer");
    const pagination = document.getElementById("pagination");
    let currentPage = 1;

    async function fetchPosts(tags = "", page = 1) {
      try {
        const url = `${apiBase}&json=1&tags=${tags}&pid=${page - 1}&limit=20`;
        const response = await fetch(url);
        const data = await response.json();
        renderPosts(data);
      } catch (error) {
        console.error("Error fetching posts:", error);
        postContainer.innerHTML = "<p class='text-danger'>Failed to load posts.</p>";
      }
    }

    function renderPosts(posts) {
      postContainer.innerHTML = "";
      if (!posts || posts.length === 0) {
        postContainer.innerHTML = "<p>No results found.</p>";
        return;
      }
      posts.forEach(post => {
        const postHTML = `
          <div class="col-md-3 post">
            <div class="card">
              <img src="${post.preview_url}" alt="Post Preview" class="card-img-top">
              <div class="card-body">
                <h5 class="card-title">Post ID: ${post.id}</h5>
                <a href="${post.file_url}" target="_blank" class="btn btn-primary btn-sm">View Full Image</a>
              </div>
            </div>
          </div>`;
        postContainer.insertAdjacentHTML("beforeend", postHTML);
      });
    }

    function setupPagination(tags) {
      pagination.innerHTML = `
        <li class="page-item ${currentPage === 1 ? "disabled" : ""}">
          <a class="page-link" href="#" data-page="${currentPage - 1}">Previous</a>
        </li>
        <li class="page-item">
          <a class="page-link" href="#" data-page="${currentPage + 1}">Next</a>
        </li>`;
    }

    document.getElementById("searchForm").addEventListener("submit", (e) => {
      e.preventDefault();
      const tags = document.getElementById("tags").value;
      currentPage = 1;
      fetchPosts(tags, currentPage);
      setupPagination(tags);
    });

    pagination.addEventListener("click", (e) => {
      if (e.target.tagName === "A") {
        e.preventDefault();
        const tags = document.getElementById("tags").value;
        const page = parseInt(e.target.getAttribute("data-page"), 10);
        if (page > 0) {
          currentPage = page;
          fetchPosts(tags, currentPage);
          setupPagination(tags);
        }
      }
    });

    // Initial fetch
    fetchPosts();
  </script>
</body>
</html>

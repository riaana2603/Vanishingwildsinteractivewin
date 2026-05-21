# Vanishingwildsinteractivewin
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>You Chose to Care</title>

<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@500&family=Inter:wght@300;400&display=swap" rel="stylesheet">

<style>
body {
    margin: 0;
    font-family: 'Inter', sans-serif;
    color: white;
    text-align: center;

    background:
        linear-gradient(rgba(0,0,0,0.35), rgba(0,0,0,0.55)),
        url('https://images.unsplash.com/photo-1501785888041-af3ef285b470');

    background-size: cover;
    background-position: center;
    background-attachment: fixed;
}

.container {
    margin-top: 120px;
}

h1 {
    font-family: 'Playfair Display', serif;
    font-size: 48px;
}

.info {
    max-width: 500px;
    margin: 10px auto;
    opacity: 0.85;
}

button {
    margin: 10px;
    padding: 10px 18px;
    border-radius: 20px;
    border: none;
    background: rgba(255,255,255,0.2);
    color: white;
    cursor: pointer;
}

/* SECTIONS */
.section {
    max-width: 700px;
    margin: 40px auto;
    text-align: left;
    padding: 15px;
    background: rgba(0,0,0,0.35);
    border-radius: 12px;
}

.item {
    padding: 10px;
    margin: 6px 0;
    background: rgba(255,255,255,0.08);
    border-radius: 8px;
}

/* POSTS */
.post {
    padding: 10px;
    margin: 10px 0;
    background: rgba(255,255,255,0.08);
    border-radius: 10px;
}

.small {
    font-size: 12px;
    opacity: 0.8;
}
</style>
</head>

<body>

<div class="container">
    <h1>You Chose to Care</h1>

    <p class="info">
        Wildfires are destroying habitats. A few people choose to act.
    </p>

    <div id="count">0 people</div>

    <button onclick="createAccount()">Create Account</button>
    <button onclick="join()">Join</button>
    <button onclick="adminLogin()">Admin Mode</button>
</div>

<!-- PEOPLE -->
<div class="section">
    <h2>People Who Care</h2>
    <div id="people"></div>
</div>

<!-- POSTS -->
<div class="section">
    <h2>Community Posts</h2>

    <input id="postInput" placeholder="Write something..."
        style="width:70%; padding:10px; border-radius:10px; border:none;">

    <button onclick="addPost()">Post</button>

    <div id="posts"></div>
</div>

<script>
const PASSWORD = "care2026";

let account = JSON.parse(localStorage.getItem("account") || "null");
let users = JSON.parse(localStorage.getItem("users") || "[]");
let posts = JSON.parse(localStorage.getItem("posts") || "[]");

let isAdmin = false;

/* SAVE */
function saveUsers() {
    localStorage.setItem("users", JSON.stringify(users));
}

function savePosts() {
    localStorage.setItem("posts", JSON.stringify(posts));
    renderPosts();
}

/* ACCOUNT */
function createAccount() {
    if (account && !isAdmin) {
        alert("You already have an account on this device.");
        return;
    }

    let name = prompt("Create username:");
    if (!name) return;

    account = { name: name.trim() };
    localStorage.setItem("account", JSON.stringify(account));

    alert("Logged in as " + account.name);
}

/* ADMIN */
function adminLogin() {
    let p = prompt("Enter password:");
    if (p === PASSWORD) {
        isAdmin = true;
        alert("Admin mode enabled");
    }
}

/* JOIN */
function join() {
    if (!account && !isAdmin) {
        alert("Create an account first.");
        return;
    }

    if (!isAdmin) {
        if (users.some(u => u.name === account.name)) {
            alert("You already joined.");
            return;
        }
    }

    let action = prompt("What will you do to help?");

    users.push({
        name: account ? account.name : "Admin",
        action: action || ""
    });

    saveUsers();
    renderUsers();
}

/* USERS */
function renderUsers() {
    document.getElementById("count").innerText = users.length + " people";

    const box = document.getElementById("people");
    box.innerHTML = "";

    users.forEach(u => {
        const div = document.createElement("div");
        div.className = "item";

        div.innerHTML = `
            <b>${u.name}</b><br>
            <span class="small">${u.action || "No action added"}</span>
        `;

        box.appendChild(div);
    });
}

/* POSTS */
function addPost() {
    if (!account && !isAdmin) {
        alert("Create account first");
        return;
    }

    let text = document.getElementById("postInput").value;
    if (!text.trim()) return;

    posts.push({
        user: account ? account.name : "Admin",
        text: text.trim(),
        likes: 0,
        likedBy: [],
        comments: []
    });

    document.getElementById("postInput").value = "";
    savePosts();
}

/* LIKE / UNLIKE FIXED */
function likePost(i) {
    if (!account || !account.name) {
        alert("Create an account first to like posts.");
        return;
    }

    let p = posts[i];
    let user = account.name;

    if (!p.likedBy) p.likedBy = [];

    if (p.likedBy.includes(user)) {
        p.likedBy = p.likedBy.filter(x => x !== user);
    } else {
        p.likedBy.push(user);
    }

    p.likes = p.likedBy.length;

    savePosts();
}

/* COMMENT */
function commentPost(i) {
    let c = prompt("Comment:");
    if (!c) return;

    posts[i].comments.push({
        user: account ? account.name : "Anonymous",
        text: c
    });

    savePosts();
}

/* RENDER POSTS */
function renderPosts() {
    const box = document.getElementById("posts");
    box.innerHTML = "";

    posts.forEach((p, i) => {
        const div = document.createElement("div");
        div.className = "post";

        div.innerHTML = `
            <b>${p.user}</b><br>
            ${p.text}<br><br>

            ❤️ ${p.likes}
            <button onclick="likePost(${i})">
                ${account && p.likedBy.includes(account.name) ? "Unlike" : "Like"}
            </button>
            <button onclick="commentPost(${i})">Comment</button>

            <div class="small">
                ${p.comments.map(c => `<div><b>${c.user}:</b> ${c.text}</div>`).join("")}
            </div>
        `;

        box.appendChild(div);
    });
}

/* INIT */
renderUsers();
renderPosts();
</script>

</body>
</html>

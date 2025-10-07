const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors');
const fs = require('fs-extra');
const path = require('path');

const app = express();
const PORT = 3000;
const DATA_FILE = path.join(__dirname, 'data', 'posts.json');

app.use(cors());
app.use(bodyParser.json());
app.use(express.static(path.join(__dirname, 'public')));

// Helper: read posts
async function getPosts() {
  try {
    return await fs.readJSON(DATA_FILE);
  } catch (err) {
    return [];
  }
}

// Helper: save posts
async function savePosts(posts) {
  await fs.writeJSON(DATA_FILE, posts, { spaces: 2 });
}

// Serve homepage
app.get('/', (req, res) => {
  res.sendFile(path.join(__dirname, 'public', 'index.html'));
});

// API: Get all posts
app.get('/api/posts', async (req, res) => {
  const posts = await getPosts();
  res.json(posts);
});

// API: Get single post by id
app.get('/api/posts/:id', async (req, res) => {
  const posts = await getPosts();
  const post = posts.find(p => p.id === parseInt(req.params.id));
  if (!post) return res.status(404).json({ error: 'Post not found' });
  res.json(post);
});

// API: Add a new post
app.post('/api/posts', async (req, res) => {
  const { title, author, content } = req.body;
  if (!title || !content) return res.status(400).json({ error: 'Title and content required' });

  const posts = await getPosts();
  const newPost = { id: Date.now(), title, author, content, comments: [] };
  posts.push(newPost);
  await savePosts(posts);
  res.json(newPost);
});

// API: Add a comment to a post
app.post('/api/posts/:id/comments', async (req, res) => {
  const { username, text } = req.body;
  if (!text) return res.status(400).json({ error: 'Comment text required' });

  const posts = await getPosts();
  const post = posts.find(p => p.id === parseInt(req.params.id));
  if (!post) return res.status(404).json({ error: 'Post not found' });

  post.comments.push({ username: username || 'Anonymous', text });
  await savePosts(posts);
  res.json(post);
});

// Catch-all route: serve index.html for deep links (optional for SPA)
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, 'public', 'index.html'));
});

app.listen(PORT, () => console.log(`Server running at http://localhost:${PORT}`));

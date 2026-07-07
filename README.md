const http = require('http');
const httpProxy = require('http-proxy');

const proxy = httpProxy.createProxyServer({});

proxy.on('error', function (err, req, res) {
  res.writeHead(500, { 'Content-Type': 'text/plain' });
  res.end('Proxy error: Something went wrong.');
});

const server = http.createServer((req, res) => {
  const urlObj = new URL(req.url, `http://${req.headers.host}`);
  const targetUrl = urlObj.searchParams.get('url');

  if (req.url.startsWith('/proxy') && targetUrl) {
    proxy.web(req, res, { target: targetUrl, changeOrigin: true });
  } else {
    res.writeHead(200, { 'Content-Type': 'text/html' });
    res.end(`
      <h1>Custom School Proxy Dashboard</h1>
      <p>To access an app or site, format your URL like this:</p>
      <code>https://onrender.com</code>
    `);
  }
});

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
  console.log(`Proxy running smoothly on port ${PORT}`);
});

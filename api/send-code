module.exports = async function handler(req, res) {
  if (req.method !== "POST") {
    return res.status(405).json({ error: "Method not allowed" });
  }

  const { email } = req.body;

  if (!email || !email.includes("@")) {
    return res.status(400).json({ error: "Email inválido" });
  }

  try {
    const response = await fetch(`https://${process.env.AUTH0_DOMAIN}/passwordless/start`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        client_id: process.env.AUTH0_CLIENT_ID,
        client_secret: process.env.AUTH0_CLIENT_SECRET,
        connection: "email",
        email: email,
        send: "code"
      })
    });

    const data = await response.json();

    if (response.ok) {
      return res.status(200).json({ success: true });
    } else {
      console.error("Auth0 error:", data);
      return res.status(400).json({ error: data.error_description || "Erro ao enviar código" });
    }
  } catch (e) {
    console.error("Server error:", e);
    return res.status(500).json({ error: "Erro de servidor" });
  }
};

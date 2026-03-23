module.exports = async function handler(req, res) {
  if (req.method !== "POST") {
    return res.status(405).json({ error: "Método não permitido" });
  }

  try {
    const { email, code } = req.body || {};

    // ================================
    // VALIDAÇÃO
    // ================================
    if (!email || !email.includes("@")) {
      return res.status(400).json({ error: "Email inválido" });
    }

    if (!code || String(code).length !== 6) {
      return res.status(400).json({ error: "Código inválido" });
    }

    // ================================
    // REQUEST AUTH0
    // ================================
    const auth0Res = await fetch(
      `https://${process.env.AUTH0_DOMAIN}/oauth/token`,
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          grant_type: "http://auth0.com/oauth/grant-type/passwordless/otp",
          client_id: process.env.AUTH0_CLIENT_ID,
          client_secret: process.env.AUTH0_CLIENT_SECRET,
          username: email,
          otp: code,
          realm: "email",
          scope: "openid email profile"
        })
      }
    );

    const data = await auth0Res.json();

    // ================================
    // TRATAMENTO DE ERRO
    // ================================
    if (!auth0Res.ok) {
      console.error("Auth0 error:", data);

      return res.status(401).json({
        error:
          data.error_description ||
          data.error ||
          "Código inválido ou expirado"
      });
    }

    // ================================
    // SUCESSO
    // ================================
    if (data.access_token) {
      return res.status(200).json({
        success: true
        // opcional: não retornar token por segurança
      });
    }

    // fallback raro
    return res.status(401).json({
      error: "Falha na autenticação"
    });

  } catch (err) {
    console.error("Server error:", err);

    return res.status(500).json({
      error: "Erro interno do servidor"
    });
  }
};

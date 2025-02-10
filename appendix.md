# Appendix





## Connect Domain to Server IP

1. **Get Your Server IP**
   * Find the public IP of your server (`IPv4` or `IPv6`).
2. **Access Domain DNS Settings**
   * Log into your domain registrar (e.g., Namecheap, GoDaddy, Cloudflare).
3. **Create an A Record** (For IPv4)
   * Set:
     * **Type:** A
     * **Host:** `@` (or subdomain, e.g., `www`)
     * **Value:** Your server's IPv4
     * **TTL:** Default (Auto or 300s)
4. **Create an AAAA Record** (For IPv6, optional)
   * Same as A record, but with IPv6 address.
5. **Update Nameservers** (If using custom DNS like Cloudflare)
   * Change NS records at the registrar to point to your DNS provider.
6. **Verify Propagation**
   * Use `nslookup domain.com` or [whatsmydns.net](https://www.whatsmydns.net/).
7. **Configure Web Server (Optional)**
   * Ensure NGINX/Apache listens to the domain.
8. **Enable SSL (Optional, Recommended)**
   * Use Let's Encrypt (`certbot`) or a commercial SSL.&#x20;
9. **Test Everything**
   * Open the domain in a browser.
   * Check with `ping domain.com`.

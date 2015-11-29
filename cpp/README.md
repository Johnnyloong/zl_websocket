C++版 WebSocketServer
==============

实现在[zl_reactor/zlreactor/net/websocket](https://github.com/lizhenghn123/zl_reactor)。

使用方式：

	class EchoWebServer
	{
	public:
	    EchoWebServer(EventLoop *loop, const InetAddress& listenAddr)
	        : server_(loop, listenAddr, "EchoWebServer")
	    {
	        server_.setOnOpen(std::bind(&EchoWebServer::onopen, this, std::placeholders::_1));
	        server_.setOnClose(std::bind(&EchoWebServer::onclose, this, std::placeholders::_1));
	        server_.setOnMessage(std::bind(&EchoWebServer::onmessage, this, std::placeholders::_1,
	                                std::placeholders::_2, std::placeholders::_3));
	    }
	
	    void start()
	    {
	        server_.start();
	    }
	
	private:
	    void onopen(const TcpConnectionPtr& conn)
	    {
	        assert(conn->connected());
	        cout << "EchoWebServer " << conn->fd() << " connected\n";
	    }
	
	    void onclose(const TcpConnectionPtr& conn)
	    {
	        cout << "EchoWebServer " << conn->fd() << " disconnected\n";
	    }
	
	    void onmessage(const TcpConnectionPtr& conn, const std::vector<char>& buf, Timestamp)
	    {
	         cout << "EchoWebServer onmessage : " << buf.data() << "\n";
	         //WsConnection *wsconn = zl::stl::any_cast<WsConnection>(conn->getMutableContext());
	         //server->sendText(buf.data(), buf.size());
	         server_.send(conn, buf.data(), buf.size());
	    }
	
	private:
	    zl::net::ws::WsServer server_;
	};
	
	
	int main()
	{
	    //LOG_DISABLE_ALL;
	
	    EventLoop loop;
	    EchoWebServer server(&loop, InetAddress("0.0.0.0", 8888));
	
	    server.start();
	    loop.loop();
	
	    return 0;
	}

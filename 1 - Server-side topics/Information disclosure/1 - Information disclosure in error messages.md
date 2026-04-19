## Information disclosure in error messages

This lab's verbose error messages reveal that it is using a vulnerable version of a third-party framework. To solve the lab, obtain and submit the version number of this framework.

## Notes

- error page


## Resume

1. Go to any product page
2. Update the `productId` with non existent id to get an error
3. Get the Apache version


## Solve 

Go to any product page, for example : 

`/product?productId=2`

Edit the **productId** with any non existant product.

`/product?productId=X`

The error message : 

```
Internal Server Error: java.lang.NumberFormatException: For input string: "X"
	at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:67)
	at java.base/java.lang.Integer.parseInt(Integer.java:661)
	at java.base/java.lang.Integer.parseInt(Integer.java:777)
	at lab.e.u.z.n.a(Unknown Source)
	at lab.g.m.n.u.z(Unknown Source)
	at lab.g.m.g.k.g.G(Unknown Source)
	at lab.g.m.g.g.lambda$handleSubRequest$0(Unknown Source)
	at m.c.h.j.lambda$null$3(Unknown Source)
	at m.c.h.j.D(Unknown Source)
	at m.c.h.j.lambda$uncheckedFunction$4(Unknown Source)
	at java.base/java.util.Optional.map(Optional.java:260)
	at lab.g.m.g.g.o(Unknown Source)
	at lab.server.q.v.q.l(Unknown Source)
	at lab.g.m.b.P(Unknown Source)
	at lab.g.m.b.l(Unknown Source)
	at lab.server.q.v.w.p.T(Unknown Source)
	at lab.server.q.v.w.c.lambda$handle$0(Unknown Source)
	at lab.e.h.s.t.m(Unknown Source)
	at lab.server.q.v.w.c.r(Unknown Source)
	at lab.server.q.v.f.o(Unknown Source)
	at m.c.h.j.lambda$null$3(Unknown Source)
	at m.c.h.j.D(Unknown Source)
	at m.c.h.j.lambda$uncheckedFunction$4(Unknown Source)
	at lab.server.fd.d(Unknown Source)
	at lab.server.q.v.f.C(Unknown Source)
	at lab.server.q.k.f.d(Unknown Source)
	at lab.server.q.r.H(Unknown Source)
	at lab.server.q.m.H(Unknown Source)
	at lab.server.f9.w(Unknown Source)
	at lab.server.f9.U(Unknown Source)
	at lab.p.a.lambda$consume$0(Unknown Source)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1144)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:642)
	at java.base/java.lang.Thread.run(Thread.java:1583)

Apache Struts 2 2.3.31
```
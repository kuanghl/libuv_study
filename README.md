gcc -g -Ilibuv/include  main.c libuv/.libs/libuv.a
g++ -g -Ilibuv/include -Llibuv/.libs/libuv.a  main.c -o main 
-L/usr/local/lib
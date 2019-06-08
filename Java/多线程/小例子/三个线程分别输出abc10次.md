# 三个线程分别输出a,b,c...10次

## 用wait和notify实现
```
public class PrintABC { 
    private int index=0;
 
    public synchronized void print(int n){
        try {
             
            while(index!=n){
                wait();
            }
            //index=0打印a
            if(index==0)
                System.out.print("a");
            //index=1打印b
            if(index==1)
                System.out.print("b");
            //index=2打印c
            if(index==2)
                System.out.print("c");
            index = (n+1)%3;
            notifyAll();
             
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
 
 
 
public class AllThread {
    private PrintABC printABC;
    public AllThread (PrintABC printABC) {
        this.printABC=printABC;
    }
    class AThread extends Thread{
        @Override
        public void run() {
            for(int i=0;i<10;i++){
                printABC.print(0);
            }
        }
    }
     
    class BThread extends Thread{
        @Override
        public void run() {
            for(int i=0;i<10;i++){
                printABC.print(1);
            }
        }
    }
    class CThread extends Thread{
        @Override
        public void run() {
            for(int i=0;i<10;i++){
                printABC.print(2);
            }
        }
    }
     
    static class Test{
        public static void main(String[] args) {
            AllThread allThread = new AllThread(new PrintABC());
            allThread.new AThread().start();
            allThread.new BThread().start();
            allThread.new CThread().start();
        }
    }
}
```
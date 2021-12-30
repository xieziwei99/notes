- 成员函数中，默认第一个参数是 this，在调用时，也默认会将对象实例作为第一个参数传入

    ```java
    public class Main {
    
        public static void main(String[] args) throws Exception {
            Pet pet = new Pet();
    		// 调用时不需要传入，也不能传入
            pet.sayHello();
        }
    }
    
    class Pet {
        private String name = "mimi";
        private int age = 3;
    
        // 可以直接将 this 作为第一个参数写出来
        public void sayHello(Pet this) {
            System.out.println(this.age + this.name);
        }
    }
    ```

    


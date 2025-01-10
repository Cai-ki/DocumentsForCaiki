.. index:: 哲学家就餐问题（Dining Philosophers Problem）

哲学家就餐问题（Dining Philosophers Problem）
============================================

    哲学家就餐问题是一个经典的并发控制问题。假设有五位哲学家围坐在一张圆桌旁，每位哲学家面前有一盘食物，以及左右两边各有一根筷子。哲学家有两种状态：思考和就餐。就餐时需要同时拿起左右两根筷子，思考时则放下筷子。问题在于如何设计一个算法，使得哲学家们能够顺利就餐，而不会出现死锁或饥饿现象。
    
    .. code-block:: Go

        package main

        import (
            "sync"
        )

        // 哲学家数量
        const philosopherNum = 5

        // 筷子可以用互斥锁来模拟，每个筷子对应一个互斥锁
        var chopsticks [philosopherNum]sync.Mutex

        // Philosopher结构体代表哲学家
        type Philosopher struct {
            id int
        }

        // Eat方法模拟哲学家就餐的操作
        func (p *Philosopher) Eat() {
            left := p.id
            right := (p.id + 1) % philosopherNum

            // 先获取左边筷子
            chopsticks[left].Lock()
            // 再获取右边筷子，如果获取不到右边筷子，先释放左边筷子，避免死锁
            if ok := chopsticks[right].TryLock(); ok {
                println("Philosopher", p.id, "is eating.")
                chopsticks[right].Unlock()
            }
            chopsticks[left].Unlock()
        }

        func main() {
            var philosophers []*Philosopher

            // 创建5个哲学家
            for i := 0; i < philosopherNum; i++ {
                philosophers = append(philosophers, &Philosopher{id: i})
            }

            // 模拟哲学家们的就餐行为，多次循环体现持续的过程
            for j := 0; j < 10; j++ {
                for _, philosopher := range philosophers {
                    go philosopher.Eat()
                }
                time.Sleep(100 * time.Millisecond)
            }
        }
.. index:: 生产者 - 消费者模型（Producer - Consumer Model）

生产者 - 消费者模型（Producer - Consumer Model）
==============================================

    生产者 - 消费者模型是一种经典的并发编程模型。它主要涉及两个角色：生产者（Producer）和消费者（Consumer），以及一个用于存储数据的共享缓冲区（Buffer）。生产者的主要职责是生成数据，并将数据放入缓冲区；消费者则从缓冲区中取出数据进行处理。

    .. code-block:: Go

        package main

        import (
            "context"
            "fmt"
            "time"

            "github.com/gin-gonic/gin"
        )

        // TokenProducer结构体用于管理生产者相关的状态和操作
        type TokenProducer struct {
            last     time.Time
            id       int
            tokenCh  chan int
            stopChan chan struct{} // 用于接收停止信号的通道
        }

        // NewTokenProducer创建一个新的TokenProducer实例
        func NewTokenProducer() *TokenProducer {
            return &TokenProducer{
                last:     time.Now(),
                id:       1,
                tokenCh:  make(chan int, 10),
                stopChan: make(chan struct{}),
            }
        }

        // Produce方法实现生产者的核心逻辑，按一定时间间隔向通道发送令牌
        func (p *TokenProducer) Produce() {
            for {
                select {
                case <-p.stopChan:
                    // 收到停止信号，关闭tokenCh通道并退出循环
                    close(p.tokenCh)
                    return
                default:
                    if time.Since(p.last).Seconds() >= 1 {
                        p.tokenCh <- p.id
                        p.id++
                        p.last = time.Now()
                    }
                }
            }
        }

        // Stop方法用于向生产者发送停止信号
        func (p *TokenProducer) Stop() {
            p.stopChan <- struct{}{}
        }

        // TokenConsumer函数作为消费者，从通道获取令牌并处理
        func TokenConsumer(ctx *gin.Context, tokenCh chan int) {
            select {
            case id, ok := <-tokenCh:
                if ok {
                    ctx.JSON(200, gin.H{
                        "token": id,
                    })
                } else {
                    ctx.JSON(200, gin.H{
                        "message": "Token channel is closed",
                    })
                }
            default:
                ctx.JSON(200, gin.H{
                    "message": "No token available",
                })
            }
        }

        func main() {
            r := gin.Default()
            producer := NewTokenProducer()

            // 启动生产者协程
            go producer.Produce()

            // 定义一个路由，用于获取令牌
            r.GET("/", func(ctx *gin.Context) {
                TokenConsumer(ctx, producer.tokenCh)
            })

            // 模拟程序运行一段时间后停止，这里设置为运行10秒后停止
            ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
            defer cancel()

            go func() {
                <-ctx.Done()
                producer.Stop()
                fmt.Println("Producer stopped")
            }()

            r.Run()
        }
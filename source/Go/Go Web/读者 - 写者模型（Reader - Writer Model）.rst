.. index:: 读者 - 写者模型（Reader - Writer Model）

读者 - 写者模型（Reader - Writer Model）
========================================

    读者 - 写者模型主要用于处理对共享资源（如文件、数据库记录或内存中的数据结构）的并发访问。在这个模型中，有两种角色：读者和写者。读者可以同时对共享资源进行读取操作，而写者在对共享资源进行写操作时，需要独占访问权，即不允许其他读者或写者同时访问。

    .. code-block:: Go

        package main

        import (
            "sync"
            "time"
        )

        // 共享资源，这里简单模拟为一个整数变量
        var sharedData int

        // 读写锁，用于控制对共享资源的并发访问
        var rwMutex sync.RWMutex

        // Reader结构体代表读者
        type Reader struct {
            id int
        }

        // Writer结构体代表写者
        type Writer struct {
            id int
        }

        // Read方法模拟读者读取共享资源的操作
        func (r *Reader) Read() {
            rwMutex.RLock()
            defer rwMutex.RUnlock()
            // 这里简单打印读取到的数据，实际可以进行更复杂的处理
            println("Reader", r.id, "reads data:", sharedData)
        }

        // Write方法模拟写者写入共享资源的操作
        func (w *Writer) Write() {
            rwMutex.Lock()
            defer rwMutex.Unlock()
            sharedData++
            println("Writer", w.id, "writes data:", sharedData)
        }

        func main() {
            var readers []*Reader
            var writers []*Writer

            // 创建5个读者和3个写者
            for i := 1; i <= 5; i++ {
                readers = append(readers, &Reader{id: i})
            }
            for i := 1; i <= 3; i++ {
                writers = append(writers, &Writer{id: i})
            }

            // 模拟并发访问，这里简单通过循环多次来体现
            for j := 0; j < 10; j++ {
                for _, reader := range readers {
                    go reader.Read()
                }
                for _, writer := range writers {
                    go writer.Write()
                }
                time.Sleep(100 * time.Millisecond)
            }
        }

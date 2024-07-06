Part 1：understandable raytracing（第 1 部分：易于理解的光线追踪）
=======================================================================

Step 1：写入图片
********************
    
    ::

        构造PPM格式的图片。

    .. code:: cpp

        #include <fstream>

        int main()
        {
            int width = 640, height = 480;

            std::ofstream ofs("./out.ppm", std::ios::binary);
            ofs << "P6\n"
                << width << " " << height << "\n255\n";

            for (int pix = 0; pix < width * height; pix++)
            {
                int x = pix % width;
                int y = pix / width;
                ofs << (char)(1.0 * x / width * 255)
                    << (char)(1.0 * y / height * 255)
                    << (char)(1.0 * pix / (width * height) * 255);
            }

            return 0;
        }

    .. image:: ../_static/tinyraytracer（微型光线追踪器）/写入图片.png
        :alt: 写入图片
        :align: center

Step 2：光线追踪
**********************

    ::

        实现最基础的光线追踪。
        从观察点（0，0，0）发出打向各个像素的的光线，对于每条光线判断是否与球体相交，返回不同的颜色。

    .. code:: cpp

        #include <fstream>
        #include <array>
        #include <cmath>
        #include <limits>

        struct v3f
        {
            float x = 0, y = 0, z = 0;
            float &operator[](const int i) { return i == 0 ? x : (1 == i ? y : z); }
            const float &operator[](const int i) const { return i == 0 ? x : (1 == i ? y : z); }
            v3f operator*(const float v) const { return {x * v, y * v, z * v}; }
            float operator*(const v3f &v) const { return x * v.x + y * v.y + z * v.z; }
            v3f operator+(const v3f &v) const { return {x + v.x, y + v.y, z + v.z}; }
            v3f operator-(const v3f &v) const { return {x - v.x, y - v.y, z - v.z}; }
            v3f operator-() const { return {-x, -y, -z}; }
            float norm() const { return std::sqrt(x * x + y * y + z * z); }
            v3f normalized() const { return (*this) * (1.f / norm()); }
        };

        struct Sphere
        {
            v3f center;
            float radius;
            bool ray_intersect(const v3f &orig, const v3f &dir, float &t0) const
            {
                v3f L = center - orig;
                float tca = L * dir;
                float d2 = L * L - tca * tca;
                if (d2 > radius * radius)
                    return false;
                float thc = sqrtf(radius * radius - d2);
                t0 = tca - thc;
                float t1 = tca + thc;
                if (t0 < 0)
                    t0 = t1;
                if (t0 < 0)
                    return false;
                return true;
            }
        };

        v3f cast_ray(const v3f &orig, const v3f &dir, const Sphere &sphere)
        {
            float sphere_dist = std::numeric_limits<float>::max();
            if (!sphere.ray_intersect(orig, dir, sphere_dist))
            {
                return v3f({0.2, 0.7, 0.8});
            }
            return v3f({0.4, 0.4, 0.3});
        }

        int main()
        {
            int width = 640, height = 480;
            float fov = 105. / 180. * 3.1415926;

            std::ofstream ofs("./out.ppm", std::ios::binary);
            ofs << "P6\n"
                << width << " " << height << "\n255\n";

            Sphere sphere({{0, 0, 8}, 3});

            for (int pix = 0; pix < width * height; pix++)
            {
                int i = pix % width;
                int j = pix / width;
                float x = (2. * (i + 0.5) / (float)width - 1) * tan(fov / 2.);
                float y = -(2. * (j + 0.5) / (float)height - 1) * tan(fov / 2.) * (float)height / (float)width;
                v3f dir = v3f({x, y, 1}).normalized();
                v3f v = cast_ray(v3f({0, 0, 0}), dir, sphere);
                ofs
                    << (char)(v[0] * 255)
                    << (char)(v[1] * 255)
                    << (char)(v[2] * 255);
            }

            return 0;
        }

    .. image:: ../_static/tinyraytracer（微型光线追踪器）/光线追踪.png
        :alt: 光线追踪
        :align: center

Step 3：添加更多球体
**************************

    ::

        为球体赋予材质属性，光线打向该物体时要注意只取里光线最近的球体的材质属性。
        若与最近相交的球体的距离太远认为无相交。
    
    .. code:: cpp

        #include <fstream>
        #include <vector>
        #include <array>
        #include <cmath>
        #include <limits>

        struct v3f
        {
            float x = 0, y = 0, z = 0;
            v3f() : x(), y(), z() {}
            v3f(float x, float y, float z) : x(x), y(y), z(z) {}
            float &operator[](const int i) { return i == 0 ? x : (1 == i ? y : z); }
            const float &operator[](const int i) const { return i == 0 ? x : (1 == i ? y : z); }
            v3f operator*(const float v) const { return {x * v, y * v, z * v}; }
            float operator*(const v3f &v) const { return x * v.x + y * v.y + z * v.z; }
            v3f operator+(const v3f &v) const { return {x + v.x, y + v.y, z + v.z}; }
            v3f operator-(const v3f &v) const { return {x - v.x, y - v.y, z - v.z}; }
            v3f operator-() const { return {-x, -y, -z}; }
            float norm() const { return std::sqrt(x * x + y * y + z * z); }
            v3f normalized() const { return (*this) * (1.f / norm()); }
        };

        struct Material
        {
            v3f diffuse_color;
            Material() : diffuse_color() {}
            Material(const v3f &color) : diffuse_color(color) {}
        };

        struct Sphere
        {
            v3f center;
            float radius;
            Material material;

            Sphere(const v3f &c, const float &r, const Material &m) : center(c), radius(r), material(m) {}

            bool ray_intersect(const v3f &orig, const v3f &dir, float &t0) const
            {
                v3f L = center - orig;
                float tca = L * dir;
                float d2 = L * L - tca * tca;
                if (d2 > radius * radius)
                    return false;
                float thc = sqrtf(radius * radius - d2);
                t0 = tca - thc;
                float t1 = tca + thc;
                if (t0 < 0)
                    t0 = t1;
                if (t0 < 0)
                    return false;
                return true;
            }
        };

        bool scene_intersect(const v3f &orig, const v3f &dir, const std::vector<Sphere> &spheres, v3f &hit, v3f &N, Material &material)
        {
            float spheres_dist = std::numeric_limits<float>::max();
            for (size_t i = 0; i < spheres.size(); i++)
            {
                float dist_i;
                if (spheres[i].ray_intersect(orig, dir, dist_i) && dist_i < spheres_dist)
                {
                    spheres_dist = dist_i;
                    hit = orig + dir * dist_i;
                    N = (hit - spheres[i].center).normalized();
                    material = spheres[i].material;
                }
            }
            return spheres_dist < 1000;
        }

        v3f cast_ray(const v3f &orig, const v3f &dir, const std::vector<Sphere> &spheres)
        {
            v3f point, N;
            Material material;

            if (!scene_intersect(orig, dir, spheres, point, N, material))
            {
                return v3f(0.2, 0.7, 0.8);
            }

            return material.diffuse_color;
        }

        void render(const std::vector<Sphere> &spheres)
        {
            const int width = 640, height = 480;
            const float fov = 90 / 180. * 3.1415926;

            std::ofstream ofs("./out.ppm", std::ios::binary);
            ofs << "P6\n"
                << width << " " << height << "\n255\n";

            for (size_t pix = 0; pix < width * height; pix++)
            {
                size_t i = pix % width;
                size_t j = pix / width;
                float x = (2. * (i + 0.5) / (float)width - 1) * tan(fov / 2.);
                float y = -(2. * (j + 0.5) / (float)height - 1) * tan(fov / 2.) * (float)height / (float)width;
                v3f dir = v3f({x, y, 1}).normalized();
                v3f v = cast_ray(v3f({0, 0, 0}), dir, spheres);
                ofs
                    << (char)(v[0] * 255)
                    << (char)(v[1] * 255)
                    << (char)(v[2] * 255);
            }
        }

        int main()
        {

            Material ivory(v3f(0.4, 0.4, 0.3));
            Material red_rubber(v3f(0.3, 0.1, 0.1));

            std::vector<Sphere> spheres;
            spheres.push_back(Sphere(v3f(1, 0, 16), 2, ivory));
            spheres.push_back(Sphere(v3f(0, 1.5, 12), 2, red_rubber));
            spheres.push_back(Sphere(v3f(-0.5, -0.5, 18), 3, red_rubber));
            spheres.push_back(Sphere(v3f(4, 5, 18), 4, ivory));

            render(spheres);

            return 0;
        }
    
    .. image:: ../_static/tinyraytracer（微型光线追踪器）/添加更多球体.png
        :alt: 添加更多球体
        :align: center

Step 4：照明
*********************

    ::

        对于每个交点要与光源的坐标等属性进行运算来计算光线的颜色。
        每个点的光照强弱与否和该点的法线与光源到该点的直线的角度有关。

    .. code:: cpp

        #include <fstream>
        #include <vector>
        #include <array>
        #include <cmath>
        #include <limits>

        struct v3f
        {
            float x = 0, y = 0, z = 0;
            v3f() : x(), y(), z() {}
            v3f(float x, float y, float z) : x(x), y(y), z(z) {}
            float &operator[](const int i) { return i == 0 ? x : (1 == i ? y : z); }
            const float &operator[](const int i) const { return i == 0 ? x : (1 == i ? y : z); }
            v3f operator*(const float v) const { return {x * v, y * v, z * v}; }
            float operator*(const v3f &v) const { return x * v.x + y * v.y + z * v.z; }
            v3f operator+(const v3f &v) const { return {x + v.x, y + v.y, z + v.z}; }
            v3f operator-(const v3f &v) const { return {x - v.x, y - v.y, z - v.z}; }
            v3f operator-() const { return {-x, -y, -z}; }
            float norm() const { return std::sqrt(x * x + y * y + z * z); }
            v3f normalized() const { return (*this) * (1.f / norm()); }
        };

        struct Material
        {
            v3f diffuse_color;
            Material() : diffuse_color() {}
            Material(const v3f &color) : diffuse_color(color) {}
        };

        struct Sphere
        {
            v3f center;
            float radius;
            Material material;

            Sphere(const v3f &c, const float &r, const Material &m) : center(c), radius(r), material(m) {}

            bool ray_intersect(const v3f &orig, const v3f &dir, float &t0) const
            {
                v3f L = center - orig;
                float tca = L * dir;
                float d2 = L * L - tca * tca;
                if (d2 > radius * radius)
                    return false;
                float thc = sqrtf(radius * radius - d2);
                t0 = tca - thc;
                float t1 = tca + thc;
                if (t0 < 0)
                    t0 = t1;
                if (t0 < 0)
                    return false;
                return true;
            }
        };

        struct Light
        {
            Light(const v3f &p, const float &i) : position(p), intensity(i) {}
            v3f position;
            float intensity;
        };

        bool scene_intersect(const v3f &orig, const v3f &dir, const std::vector<Sphere> &spheres, v3f &hit, v3f &N, Material &material)
        {
            float spheres_dist = std::numeric_limits<float>::max();
            for (size_t i = 0; i < spheres.size(); i++)
            {
                float dist_i;
                if (spheres[i].ray_intersect(orig, dir, dist_i) && dist_i < spheres_dist)
                {
                    spheres_dist = dist_i;
                    hit = orig + dir * dist_i;
                    N = (hit - spheres[i].center).normalized();
                    material = spheres[i].material;
                }
            }
            return spheres_dist < 1000;
        }

        v3f cast_ray(const v3f &orig, const v3f &dir, const std::vector<Sphere> &spheres, const std::vector<Light> &lights)
        {
            v3f point, N;
            Material material;

            if (!scene_intersect(orig, dir, spheres, point, N, material))
            {
                return v3f(0.2, 0.7, 0.8);
            }

            float diffuse_light_intensity = 0;
            for (size_t i = 0; i < lights.size(); i++)
            {
                v3f light_dir = (lights[i].position - point).normalized();
                diffuse_light_intensity += lights[i].intensity * std::max(0.f, light_dir * N);
            }

            return material.diffuse_color * diffuse_light_intensity;
        }

        void render(const std::vector<Sphere> &spheres, const std::vector<Light> &lights)
        {
            const int width = 640, height = 480;
            const float fov = 90 / 180. * 3.1415926;

            std::ofstream ofs("./out.ppm", std::ios::binary);
            ofs << "P6\n"
                << width << " " << height << "\n255\n";

            for (size_t pix = 0; pix < width * height; pix++)
            {
                size_t i = pix % width;
                size_t j = pix / width;
                float x = (2. * (i + 0.5) / (float)width - 1) * tan(fov / 2.);
                float y = -(2. * (j + 0.5) / (float)height - 1) * tan(fov / 2.) * (float)height / (float)width;
                v3f dir = v3f({x, y, 1}).normalized();
                v3f v = cast_ray(v3f({0, 0, 0}), dir, spheres, lights);
                ofs
                    << (char)(v[0] * 255)
                    << (char)(v[1] * 255)
                    << (char)(v[2] * 255);
            }
        }

        int main()
        {

            Material ivory(v3f(0.4, 0.4, 0.3));
            Material red_rubber(v3f(0.3, 0.1, 0.1));

            std::vector<Sphere> spheres;
            spheres.push_back(Sphere(v3f(-1, -1, 12), 2, ivory));
            spheres.push_back(Sphere(v3f(-2, 0.5, 8), 2, red_rubber));
            spheres.push_back(Sphere(v3f(-2.5, -1.5, 14), 3, red_rubber));
            spheres.push_back(Sphere(v3f(2, 4, 14), 4, ivory));

            std::vector<Light> lights;
            lights.push_back(Light(v3f(0, 20, -20), 1.5));

            render(spheres, lights);

            return 0;
        }
    
    .. image:: ../_static/tinyraytracer（微型光线追踪器）/照明.png
        :alt: 照明
        :align: center

Step 5：镜面照明
*********************

    `Phong 反射模型 <https://en.wikipedia.org/wiki/Phong_reflection_model>`_

    .. code:: cpp

        #include <fstream>
        #include <vector>
        #include <array>
        #include <cmath>
        #include <limits>

        struct v2f
        {
            float x = 0, y = 0;
            v2f() : x(), y() {}
            v2f(float x, float y) : x(x), y(y) {}
            float &operator[](const int i) { return i == 0 ? x : y; }
            const float &operator[](const int i) const { return i == 0 ? x : y; }
            v2f operator*(const float v) const { return {x * v, y * v}; }
            float operator*(const v2f &v) const { return x * v.x + y * v.y; }
            v2f operator+(const v2f &v) const { return {x + v.x, y + v.y}; }
            v2f operator-(const v2f &v) const { return {x - v.x, y - v.y}; }
            v2f operator-() const { return {-x, -y}; }
            float norm() const { return std::sqrt(x * x + y * y); }
            v2f normalized() const { return (*this) * (1.f / norm()); }
        };

        struct v3f
        {
            float x = 0, y = 0, z = 0;
            v3f() : x(), y(), z() {}
            v3f(float x, float y, float z) : x(x), y(y), z(z) {}
            float &operator[](const int i) { return i == 0 ? x : (1 == i ? y : z); }
            const float &operator[](const int i) const { return i == 0 ? x : (1 == i ? y : z); }
            v3f operator*(const float v) const { return {x * v, y * v, z * v}; }
            float operator*(const v3f &v) const { return x * v.x + y * v.y + z * v.z; }
            v3f operator+(const v3f &v) const { return {x + v.x, y + v.y, z + v.z}; }
            v3f operator-(const v3f &v) const { return {x - v.x, y - v.y, z - v.z}; }
            v3f operator-() const { return {-x, -y, -z}; }
            float norm() const { return std::sqrt(x * x + y * y + z * z); }
            v3f normalized() const { return (*this) * (1.f / norm()); }
        };

        struct Material
        {
            Material(const v2f &a, const v3f &color, const float &spec) : albedo(a), diffuse_color(color), specular_exponent(spec) {}
            Material() : albedo(1, 0), diffuse_color(), specular_exponent() {}
            v2f albedo;
            v3f diffuse_color;
            float specular_exponent;
        };

        struct Sphere
        {
            v3f center;
            float radius;
            Material material;

            Sphere(const v3f &c, const float &r, const Material &m) : center(c), radius(r), material(m) {}

            bool ray_intersect(const v3f &orig, const v3f &dir, float &t0) const
            {
                v3f L = center - orig;
                float tca = L * dir;
                float d2 = L * L - tca * tca;
                if (d2 > radius * radius)
                    return false;
                float thc = sqrtf(radius * radius - d2);
                t0 = tca - thc;
                float t1 = tca + thc;
                if (t0 < 0)
                    t0 = t1;
                if (t0 < 0)
                    return false;
                return true;
            }
        };

        struct Light
        {
            Light(const v3f &p, const float &i) : position(p), intensity(i) {}
            v3f position;
            float intensity;
        };

        v3f reflect(const v3f &I, const v3f &N)
        {
            return I - N * 2.f * (I * N);
        }

        bool scene_intersect(const v3f &orig, const v3f &dir, const std::vector<Sphere> &spheres, v3f &hit, v3f &N, Material &material)
        {
            float spheres_dist = std::numeric_limits<float>::max();
            for (size_t i = 0; i < spheres.size(); i++)
            {
                float dist_i;
                if (spheres[i].ray_intersect(orig, dir, dist_i) && dist_i < spheres_dist)
                {
                    spheres_dist = dist_i;
                    hit = orig + dir * dist_i;
                    N = (hit - spheres[i].center).normalized();
                    material = spheres[i].material;
                }
            }
            return spheres_dist < 1000;
        }

        v3f cast_ray(const v3f &orig, const v3f &dir, const std::vector<Sphere> &spheres, const std::vector<Light> &lights)
        {
            v3f point, N;
            Material material;

            if (!scene_intersect(orig, dir, spheres, point, N, material))
            {
                return v3f(0.2, 0.7, 0.8);
            }

            float diffuse_light_intensity = 0, specular_light_intensity = 0;
            for (size_t i = 0; i < lights.size(); i++)
            {
                v3f light_dir = (lights[i].position - point).normalized();
                diffuse_light_intensity += lights[i].intensity * std::max(0.f, light_dir * N);
                specular_light_intensity += powf(std::max(0.f, -reflect(-light_dir, N) * dir), material.specular_exponent) * lights[i].intensity;
            }

            return material.diffuse_color * diffuse_light_intensity * material.albedo[0] + v3f(1., 1., 1.) * specular_light_intensity * material.albedo[1];
        }

        void render(const std::vector<Sphere> &spheres, const std::vector<Light> &lights)
        {
            const int width = 640, height = 480;
            const float fov = 90 / 180. * 3.1415926;

            std::ofstream ofs("./out.ppm", std::ios::binary);
            ofs << "P6\n"
                << width << " " << height << "\n255\n";

            for (size_t pix = 0; pix < width * height; pix++)
            {
                size_t i = pix % width;
                size_t j = pix / width;
                float x = (2. * (i + 0.5) / (float)width - 1) * tan(fov / 2.);
                float y = -(2. * (j + 0.5) / (float)height - 1) * tan(fov / 2.) * (float)height / (float)width;
                v3f dir = v3f({x, y, 1}).normalized();
                v3f v = cast_ray(v3f({0, 0, 0}), dir, spheres, lights);
                float max = std::max(v[0], std::max(v[1], v[2]));
                if (max > 1)
                    v = v * (1.0 / max);
                ofs
                    << (char)(v[0] * 255)
                    << (char)(v[1] * 255)
                    << (char)(v[2] * 255);
            }
        }

        int main()
        {

            Material ivory(v2f(0.6, 0.3), v3f(0.4, 0.4, 0.3), 50.);
            Material red_rubber(v2f(0.9, 0.1), v3f(0.3, 0.1, 0.1), 10.);

            std::vector<Sphere> spheres;
            spheres.push_back(Sphere(v3f(-1, -1, 12), 2, ivory));
            spheres.push_back(Sphere(v3f(-2, 0.5, 8), 2, red_rubber));
            spheres.push_back(Sphere(v3f(1.5, -2.5, 18), 3, red_rubber));
            spheres.push_back(Sphere(v3f(2, 4, 14), 4, ivory));

            std::vector<Light> lights;
            lights.push_back(Light(v3f(-10, 20, -20), 1.5));
            lights.push_back(Light(v3f(30, -70, -25), 1.8));
            lights.push_back(Light(v3f(40, 20, -30), 1.7));

            render(spheres, lights);

            return 0;
        }

    .. image:: ../_static/tinyraytracer（微型光线追踪器）/镜面照明.png
        :alt: 镜面照明
        :align: center

Step 6：阴影
*************************

    ::

        对于每个光源，只要其和当前点之间有其它物体，就忽略当前光源的贡献。

    .. code:: cpp

        #include <fstream>
        #include <vector>
        #include <array>
        #include <cmath>
        #include <limits>

        struct v2f
        {
            float x = 0, y = 0;
            v2f() : x(), y() {}
            v2f(float x, float y) : x(x), y(y) {}
            float &operator[](const int i) { return i == 0 ? x : y; }
            const float &operator[](const int i) const { return i == 0 ? x : y; }
            v2f operator*(const float v) const { return {x * v, y * v}; }
            float operator*(const v2f &v) const { return x * v.x + y * v.y; }
            v2f operator+(const v2f &v) const { return {x + v.x, y + v.y}; }
            v2f operator-(const v2f &v) const { return {x - v.x, y - v.y}; }
            v2f operator-() const { return {-x, -y}; }
            float norm() const { return std::sqrt(x * x + y * y); }
            v2f normalized() const { return (*this) * (1.f / norm()); }
        };

        struct v3f
        {
            float x = 0, y = 0, z = 0;
            v3f() : x(), y(), z() {}
            v3f(float x, float y, float z) : x(x), y(y), z(z) {}
            float &operator[](const int i) { return i == 0 ? x : (1 == i ? y : z); }
            const float &operator[](const int i) const { return i == 0 ? x : (1 == i ? y : z); }
            v3f operator*(const float v) const { return {x * v, y * v, z * v}; }
            float operator*(const v3f &v) const { return x * v.x + y * v.y + z * v.z; }
            v3f operator+(const v3f &v) const { return {x + v.x, y + v.y, z + v.z}; }
            v3f operator-(const v3f &v) const { return {x - v.x, y - v.y, z - v.z}; }
            v3f operator-() const { return {-x, -y, -z}; }
            float norm() const { return std::sqrt(x * x + y * y + z * z); }
            v3f normalized() const { return (*this) * (1.f / norm()); }
        };

        struct Material
        {
            Material(const v2f &a, const v3f &color, const float &spec) : albedo(a), diffuse_color(color), specular_exponent(spec) {}
            Material() : albedo(1, 0), diffuse_color(), specular_exponent() {}
            v2f albedo;
            v3f diffuse_color;
            float specular_exponent;
        };

        struct Sphere
        {
            v3f center;
            float radius;
            Material material;

            Sphere(const v3f &c, const float &r, const Material &m) : center(c), radius(r), material(m) {}

            bool ray_intersect(const v3f &orig, const v3f &dir, float &t0) const
            {
                v3f L = center - orig;
                float tca = L * dir;
                float d2 = L * L - tca * tca;
                if (d2 > radius * radius)
                    return false;
                float thc = sqrtf(radius * radius - d2);
                t0 = tca - thc;
                float t1 = tca + thc;
                if (t0 < 0)
                    t0 = t1;
                if (t0 < 0)
                    return false;
                return true;
            }
        };

        struct Light
        {
            Light(const v3f &p, const float &i) : position(p), intensity(i) {}
            v3f position;
            float intensity;
        };

        v3f reflect(const v3f &I, const v3f &N)
        {
            return I - N * 2.f * (I * N);
        }

        bool scene_intersect(const v3f &orig, const v3f &dir, const std::vector<Sphere> &spheres, v3f &hit, v3f &N, Material &material)
        {
            float spheres_dist = std::numeric_limits<float>::max();
            for (size_t i = 0; i < spheres.size(); i++)
            {
                float dist_i;
                if (spheres[i].ray_intersect(orig, dir, dist_i) && dist_i < spheres_dist)
                {
                    spheres_dist = dist_i;
                    hit = orig + dir * dist_i;
                    N = (hit - spheres[i].center).normalized();
                    material = spheres[i].material;
                }
            }
            return spheres_dist < 1000;
        }

        v3f cast_ray(const v3f &orig, const v3f &dir, const std::vector<Sphere> &spheres, const std::vector<Light> &lights)
        {
            v3f point, N;
            Material material;

            if (!scene_intersect(orig, dir, spheres, point, N, material))
            {
                return v3f(0.2, 0.7, 0.8);
            }

            float diffuse_light_intensity = 0, specular_light_intensity = 0;
            for (size_t i = 0; i < lights.size(); i++)
            {
                v3f light_dir = (lights[i].position - point).normalized();

                float light_distance = (lights[i].position - point).norm();

                v3f shadow_orig = light_dir * N < 0 ? point - N * 1e-3 : point + N * 1e-3;
                v3f shadow_pt, shadow_N;
                Material tmpmaterial;
                if (scene_intersect(shadow_orig, light_dir, spheres, shadow_pt, shadow_N, tmpmaterial) && (shadow_pt - shadow_orig).norm() < light_distance)
                    continue;

                diffuse_light_intensity += lights[i].intensity * std::max(0.f, light_dir * N);
                specular_light_intensity += powf(std::max(0.f, -reflect(-light_dir, N) * dir), material.specular_exponent) * lights[i].intensity;
            }

            return material.diffuse_color * diffuse_light_intensity * material.albedo[0] + v3f(1., 1., 1.) * specular_light_intensity * material.albedo[1];
        }

        void render(const std::vector<Sphere> &spheres, const std::vector<Light> &lights)
        {
            const int width = 640, height = 480;
            const float fov = 90 / 180. * 3.1415926;

            std::ofstream ofs("./out.ppm", std::ios::binary);
            ofs << "P6\n"
                << width << " " << height << "\n255\n";

            for (size_t pix = 0; pix < width * height; pix++)
            {
                size_t i = pix % width;
                size_t j = pix / width;
                float x = (2. * (i + 0.5) / (float)width - 1) * tan(fov / 2.);
                float y = -(2. * (j + 0.5) / (float)height - 1) * tan(fov / 2.) * (float)height / (float)width;
                v3f dir = v3f({x, y, 1}).normalized();
                v3f v = cast_ray(v3f({0, 0, 0}), dir, spheres, lights);
                float max = std::max(v[0], std::max(v[1], v[2]));
                if (max > 1)
                    v = v * (1.0 / max);
                ofs
                    << (char)(v[0] * 255)
                    << (char)(v[1] * 255)
                    << (char)(v[2] * 255);
            }
        }

        int main()
        {

            Material ivory(v2f(0.6, 0.3), v3f(0.4, 0.4, 0.3), 50.);
            Material red_rubber(v2f(0.9, 0.1), v3f(0.3, 0.1, 0.1), 10.);

            std::vector<Sphere> spheres;
            spheres.push_back(Sphere(v3f(-1, -1, 12), 2, ivory));
            spheres.push_back(Sphere(v3f(-2, 0.5, 8), 2, red_rubber));
            spheres.push_back(Sphere(v3f(1.5, -2.5, 18), 3, red_rubber));
            spheres.push_back(Sphere(v3f(2, 4, 14), 4, ivory));

            std::vector<Light> lights;
            lights.push_back(Light(v3f(-10, 20, -20), 1.5));
            lights.push_back(Light(v3f(30, -70, -25), 1.8));
            lights.push_back(Light(v3f(40, 20, -30), 1.7));

            render(spheres, lights);

            return 0;
        }        

    .. image:: ../_static/tinyraytracer（微型光线追踪器）/阴影.png
        :alt: 阴影
        :align: center

Step 7：反射
********************

    ::

        求出反射光线角度，递归调用即可。

    .. code:: cpp

        #include <fstream>
        #include <vector>
        #include <array>
        #include <cmath>
        #include <limits>

        struct v2f
        {
            float x = 0, y = 0;
            v2f() : x(), y() {}
            v2f(float x, float y) : x(x), y(y) {}
            float &operator[](const int i) { return i == 0 ? x : y; }
            const float &operator[](const int i) const { return i == 0 ? x : y; }
            v2f operator*(const float v) const { return {x * v, y * v}; }
            float operator*(const v2f &v) const { return x * v.x + y * v.y; }
            v2f operator+(const v2f &v) const { return {x + v.x, y + v.y}; }
            v2f operator-(const v2f &v) const { return {x - v.x, y - v.y}; }
            v2f operator-() const { return {-x, -y}; }
            float norm() const { return std::sqrt(x * x + y * y); }
            v2f normalized() const { return (*this) * (1.f / norm()); }
        };

        struct v3f
        {
            float x = 0, y = 0, z = 0;
            v3f() : x(), y(), z() {}
            v3f(float x, float y, float z) : x(x), y(y), z(z) {}
            float &operator[](const int i) { return i == 0 ? x : (1 == i ? y : z); }
            const float &operator[](const int i) const { return i == 0 ? x : (1 == i ? y : z); }
            v3f operator*(const float v) const { return {x * v, y * v, z * v}; }
            float operator*(const v3f &v) const { return x * v.x + y * v.y + z * v.z; }
            v3f operator+(const v3f &v) const { return {x + v.x, y + v.y, z + v.z}; }
            v3f operator-(const v3f &v) const { return {x - v.x, y - v.y, z - v.z}; }
            v3f operator-() const { return {-x, -y, -z}; }
            float norm() const { return std::sqrt(x * x + y * y + z * z); }
            v3f normalized() const { return (*this) * (1.f / norm()); }
        };

        struct Material
        {
            Material(const v3f &a, const v3f &color, const float &spec) : albedo(a), diffuse_color(color), specular_exponent(spec) {}
            Material() : albedo(1, 0, 0), diffuse_color(), specular_exponent() {}
            v3f albedo;
            v3f diffuse_color;
            float specular_exponent;
        };

        struct Sphere
        {
            v3f center;
            float radius;
            Material material;

            Sphere(const v3f &c, const float &r, const Material &m) : center(c), radius(r), material(m) {}

            bool ray_intersect(const v3f &orig, const v3f &dir, float &t0) const
            {
                v3f L = center - orig;
                float tca = L * dir;
                float d2 = L * L - tca * tca;
                if (d2 > radius * radius)
                    return false;
                float thc = sqrtf(radius * radius - d2);
                t0 = tca - thc;
                float t1 = tca + thc;
                if (t0 < 0)
                    t0 = t1;
                if (t0 < 0)
                    return false;
                return true;
            }
        };

        struct Light
        {
            Light(const v3f &p, const float &i) : position(p), intensity(i) {}
            v3f position;
            float intensity;
        };

        v3f reflect(const v3f &I, const v3f &N)
        {
            return I - N * 2.f * (I * N);
        }

        bool scene_intersect(const v3f &orig, const v3f &dir, const std::vector<Sphere> &spheres, v3f &hit, v3f &N, Material &material)
        {
            float spheres_dist = std::numeric_limits<float>::max();
            for (size_t i = 0; i < spheres.size(); i++)
            {
                float dist_i;
                if (spheres[i].ray_intersect(orig, dir, dist_i) && dist_i < spheres_dist)
                {
                    spheres_dist = dist_i;
                    hit = orig + dir * dist_i;
                    N = (hit - spheres[i].center).normalized();
                    material = spheres[i].material;
                }
            }
            return spheres_dist < 1000;
        }

        v3f cast_ray(const v3f &orig, const v3f &dir, const std::vector<Sphere> &spheres, const std::vector<Light> &lights, size_t depth = 0)
        {
            v3f point, N;
            Material material;

            if (depth > 4 || !scene_intersect(orig, dir, spheres, point, N, material))
            {
                return v3f(0.2, 0.7, 0.8);
            }

            v3f reflect_dir = reflect(dir, N).normalized();
            v3f reflect_orig = reflect_dir * N < 0 ? point - N * 1e-3 : point + N * 1e-3;
            v3f reflect_color = cast_ray(reflect_orig, reflect_dir, spheres, lights, depth + 1);

            float diffuse_light_intensity = 0, specular_light_intensity = 0;
            for (size_t i = 0; i < lights.size(); i++)
            {
                v3f light_dir = (lights[i].position - point).normalized();

                float light_distance = (lights[i].position - point).norm();

                v3f shadow_orig = light_dir * N < 0 ? point - N * 1e-3 : point + N * 1e-3;
                v3f shadow_pt, shadow_N;
                Material tmpmaterial;
                if (scene_intersect(shadow_orig, light_dir, spheres, shadow_pt, shadow_N, tmpmaterial) && (shadow_pt - shadow_orig).norm() < light_distance)
                    continue;

                diffuse_light_intensity += lights[i].intensity * std::max(0.f, light_dir * N);
                specular_light_intensity += powf(std::max(0.f, -reflect(-light_dir, N) * dir), material.specular_exponent) * lights[i].intensity;
            }

            return material.diffuse_color * diffuse_light_intensity * material.albedo[0] + v3f(1., 1., 1.) * specular_light_intensity * material.albedo[1] + reflect_color * material.albedo[2];
        }

        void render(const std::vector<Sphere> &spheres, const std::vector<Light> &lights)
        {
            const int width = 640, height = 480;
            const float fov = 90 / 180. * 3.1415926;

            std::ofstream ofs("./out.ppm", std::ios::binary);
            ofs << "P6\n"
                << width << " " << height << "\n255\n";

            for (size_t pix = 0; pix < width * height; pix++)
            {
                size_t i = pix % width;
                size_t j = pix / width;
                float x = (2. * (i + 0.5) / (float)width - 1) * tan(fov / 2.);
                float y = -(2. * (j + 0.5) / (float)height - 1) * tan(fov / 2.) * (float)height / (float)width;
                v3f dir = v3f({x, y, 1}).normalized();
                v3f v = cast_ray(v3f({0, 0, 0}), dir, spheres, lights);
                float max = std::max(v[0], std::max(v[1], v[2]));
                if (max > 1)
                    v = v * (1.0 / max);
                ofs
                    << (char)(v[0] * 255)
                    << (char)(v[1] * 255)
                    << (char)(v[2] * 255);
            }
        }

        int main()
        {

            Material ivory(v3f(0.6, 0.3, 0.1), v3f(0.4, 0.4, 0.3), 50.);
            Material red_rubber(v3f(0.9, 0.1, 0.0), v3f(0.3, 0.1, 0.1), 10.);
            Material mirror(v3f(0.0, 10.0, 0.8), v3f(1.0, 1.0, 1.0), 1425.);

            std::vector<Sphere> spheres;
            spheres.push_back(Sphere(v3f(-1, -1, 12), 2, ivory));
            spheres.push_back(Sphere(v3f(-2, 0.5, 8), 2, ivory));
            spheres.push_back(Sphere(v3f(1.5, -2.5, 18), 3, red_rubber));
            spheres.push_back(Sphere(v3f(2, 4, 14), 4, mirror));

            std::vector<Light> lights;
            lights.push_back(Light(v3f(-10, 20, -20), 1.5));
            lights.push_back(Light(v3f(30, -70, -25), 1.8));
            lights.push_back(Light(v3f(40, 20, -30), 1.7));

            render(spheres, lights);

            return 0;
        }        

    .. image:: ../_static/tinyraytracer（微型光线追踪器）/反射.png
        :alt: 反射
        :align: center

Step 8：折射
******************

    `斯涅尔定律 <https://en.wikipedia.org/wiki/Snell%27s_law>`_

    .. code:: cpp

        #include <fstream>
        #include <vector>
        #include <array>
        #include <cmath>
        #include <limits>

        struct v2f
        {
            float x = 0, y = 0;
            v2f() : x(), y() {}
            v2f(float x, float y) : x(x), y(y) {}
            float &operator[](const int i) { return i == 0 ? x : y; }
            const float &operator[](const int i) const { return i == 0 ? x : y; }
            v2f operator*(const float v) const { return {x * v, y * v}; }
            float operator*(const v2f &v) const { return x * v.x + y * v.y; }
            v2f operator+(const v2f &v) const { return {x + v.x, y + v.y}; }
            v2f operator-(const v2f &v) const { return {x - v.x, y - v.y}; }
            v2f operator-() const { return {-x, -y}; }
            float norm() const { return std::sqrt(x * x + y * y); }
            v2f normalized() const { return (*this) * (1.f / norm()); }
        };

        struct v3f
        {
            float x = 0, y = 0, z = 0;
            v3f() : x(), y(), z() {}
            v3f(float x, float y, float z) : x(x), y(y), z(z) {}
            float &operator[](const int i) { return i == 0 ? x : (1 == i ? y : z); }
            const float &operator[](const int i) const { return i == 0 ? x : (1 == i ? y : z); }
            v3f operator*(const float v) const { return {x * v, y * v, z * v}; }
            float operator*(const v3f &v) const { return x * v.x + y * v.y + z * v.z; }
            v3f operator+(const v3f &v) const { return {x + v.x, y + v.y, z + v.z}; }
            v3f operator-(const v3f &v) const { return {x - v.x, y - v.y, z - v.z}; }
            v3f operator-() const { return {-x, -y, -z}; }
            float norm() const { return std::sqrt(x * x + y * y + z * z); }
            v3f normalized() const { return (*this) * (1.f / norm()); }
        };

        struct v4f
        {
            std::array<float, 4> a;
            v4f(float a0, float a1, float a2, float a3) : a({a0, a1, a2, a3}) {}
            float &operator[](const int i) { return a[i]; }
            const float &operator[](const int i) const { return a[i]; }
        };

        struct Material
        {
            float refractive_index;
            v4f albedo;
            v3f diffuse_color;
            float specular_exponent;
            Material(const float &r, const v4f &a, const v3f &color, const float &spec) : refractive_index(r), albedo(a), diffuse_color(color), specular_exponent(spec) {}
            Material() : refractive_index(1), albedo(1, 0, 0, 0), diffuse_color(), specular_exponent() {}
        };

        struct Sphere
        {
            v3f center;
            float radius;
            Material material;

            Sphere(const v3f &c, const float &r, const Material &m) : center(c), radius(r), material(m) {}

            bool ray_intersect(const v3f &orig, const v3f &dir, float &t0) const
            {
                v3f L = center - orig;
                float tca = L * dir;
                float d2 = L * L - tca * tca;
                if (d2 > radius * radius)
                    return false;
                float thc = sqrtf(radius * radius - d2);
                t0 = tca - thc;
                float t1 = tca + thc;
                if (t0 < 0)
                    t0 = t1;
                if (t0 < 0)
                    return false;
                return true;
            }
        };

        struct Light
        {
            Light(const v3f &p, const float &i) : position(p), intensity(i) {}
            v3f position;
            float intensity;
        };

        v3f reflect(const v3f &I, const v3f &N)
        {
            return I - N * 2.f * (I * N);
        }

        v3f refract(const v3f &I, const v3f &N, const float &refractive_index)
        {
            float cosi = -std::max(-1.f, std::min(1.f, I * N));
            float etai = 1, etat = refractive_index;
            v3f n = N;
            if (cosi < 0)
            {
                cosi = -cosi;
                std::swap(etai, etat);
                n = -N;
            }
            float eta = etai / etat;
            float k = 1 - eta * eta * (1 - cosi * cosi);
            return k < 0 ? v3f(0, 0, 0) : I * eta + n * (eta * cosi - sqrtf(k));
        }

        bool scene_intersect(const v3f &orig, const v3f &dir, const std::vector<Sphere> &spheres, v3f &hit, v3f &N, Material &material)
        {
            float spheres_dist = std::numeric_limits<float>::max();
            for (size_t i = 0; i < spheres.size(); i++)
            {
                float dist_i;
                if (spheres[i].ray_intersect(orig, dir, dist_i) && dist_i < spheres_dist)
                {
                    spheres_dist = dist_i;
                    hit = orig + dir * dist_i;
                    N = (hit - spheres[i].center).normalized();
                    material = spheres[i].material;
                }
            }
            return spheres_dist < 1000;
        }

        v3f cast_ray(const v3f &orig, const v3f &dir, const std::vector<Sphere> &spheres, const std::vector<Light> &lights, size_t depth = 0)
        {
            v3f point, N;
            Material material;

            if (depth > 4 || !scene_intersect(orig, dir, spheres, point, N, material))
            {
                return v3f(0.2, 0.7, 0.8);
            }

            v3f reflect_dir = reflect(dir, N).normalized();
            v3f refract_dir = refract(dir, N, material.refractive_index).normalized();
            v3f reflect_orig = reflect_dir * N < 0 ? point - N * 1e-3 : point + N * 1e-3;
            v3f refract_orig = refract_dir * N < 0 ? point - N * 1e-3 : point + N * 1e-3;
            v3f reflect_color = cast_ray(reflect_orig, reflect_dir, spheres, lights, depth + 1);
            v3f refract_color = cast_ray(refract_orig, refract_dir, spheres, lights, depth + 1);

            float diffuse_light_intensity = 0, specular_light_intensity = 0;
            for (size_t i = 0; i < lights.size(); i++)
            {
                v3f light_dir = (lights[i].position - point).normalized();

                float light_distance = (lights[i].position - point).norm();

                v3f shadow_orig = light_dir * N < 0 ? point - N * 1e-3 : point + N * 1e-3;
                v3f shadow_pt, shadow_N;
                Material tmpmaterial;
                if (scene_intersect(shadow_orig, light_dir, spheres, shadow_pt, shadow_N, tmpmaterial) && (shadow_pt - shadow_orig).norm() < light_distance)
                    continue;

                diffuse_light_intensity += lights[i].intensity * std::max(0.f, light_dir * N);
                specular_light_intensity += powf(std::max(0.f, -reflect(-light_dir, N) * dir), material.specular_exponent) * lights[i].intensity;
            }

            return material.diffuse_color * diffuse_light_intensity * material.albedo[0] + v3f(1., 1., 1.) * specular_light_intensity * material.albedo[1] + reflect_color * material.albedo[2] + refract_color * material.albedo[3];
        }

        void render(const std::vector<Sphere> &spheres, const std::vector<Light> &lights)
        {
            const int width = 640, height = 480;
            const float fov = 90 / 180. * 3.1415926;

            std::ofstream ofs("./out.ppm", std::ios::binary);
            ofs << "P6\n"
                << width << " " << height << "\n255\n";

            for (size_t pix = 0; pix < width * height; pix++)
            {
                size_t i = pix % width;
                size_t j = pix / width;
                float x = (2. * (i + 0.5) / (float)width - 1) * tan(fov / 2.);
                float y = -(2. * (j + 0.5) / (float)height - 1) * tan(fov / 2.) * (float)height / (float)width;
                v3f dir = v3f({x, y, 1}).normalized();
                v3f v = cast_ray(v3f({0, 0, 0}), dir, spheres, lights);
                float max = std::max(v[0], std::max(v[1], v[2]));
                if (max > 1)
                    v = v * (1.0 / max);
                ofs
                    << (char)(v[0] * 255)
                    << (char)(v[1] * 255)
                    << (char)(v[2] * 255);
            }
        }

        int main()
        {

            Material ivory(1.0, v4f(0.6, 0.3, 0.1, 0.0), v3f(0.4, 0.4, 0.3), 50.);
            Material glass(1.5, v4f(0.0, 0.5, 0.1, 0.8), v3f(0.6, 0.7, 0.8), 125.);
            Material red_rubber(1.0, v4f(0.9, 0.1, 0.0, 0.0), v3f(0.3, 0.1, 0.1), 10.);
            Material mirror(1.0, v4f(0.0, 10.0, 0.8, 0.0), v3f(1.0, 1.0, 1.0), 1425.);

            std::vector<Sphere> spheres;
            spheres.push_back(Sphere(v3f(-1, -1, 12), 2, ivory));
            spheres.push_back(Sphere(v3f(-2, 0.5, 8), 2, ivory));
            spheres.push_back(Sphere(v3f(1.5, -2.5, 18), 3, red_rubber));
            spheres.push_back(Sphere(v3f(2, 4, 14), 4, mirror));
            spheres.push_back(Sphere(v3f(-1, -1.5, 5), 1, glass));

            std::vector<Light> lights;
            lights.push_back(Light(v3f(-10, 20, -20), 1.5));
            lights.push_back(Light(v3f(30, -70, -25), 1.8));
            lights.push_back(Light(v3f(40, 20, -30), 1.7));

            render(spheres, lights);

            return 0;
        }       

    .. image:: ../_static/tinyraytracer（微型光线追踪器）/折射.png
        :alt: 折射
        :align: center

最终参考代码
******************

.. code:: cpp

    #include <fstream>
    #include <vector>
    #include <array>
    #include <cmath>
    #include <limits>
    #include <algorithm>

    template <size_t N>
    struct Vec
    {
        std::array<float, N> values = {};

        float &operator[](size_t i) { return values[i]; }
        const float &operator[](size_t i) const { return values[i]; }

        Vec<N> operator*(float scalar) const
        {
            Vec<N> result;
            for (size_t i = 0; i < N; ++i)
                result[i] = values[i] * scalar;
            return result;
        }

        Vec<N> operator+(const Vec<N> &vec) const
        {
            Vec<N> result;
            for (size_t i = 0; i < N; ++i)
                result[i] = values[i] + vec[i];
            return result;
        }

        Vec<N> operator-(const Vec<N> &vec) const
        {
            Vec<N> result;
            for (size_t i = 0; i < N; ++i)
                result[i] = values[i] - vec[i];
            return result;
        }

        Vec<N> operator-() const
        {
            Vec<N> result;
            for (size_t i = 0; i < N; ++i)
                result[i] = -values[i];
            return result;
        }

        float operator*(const Vec<N> &vec) const
        {
            float result = 0;
            for (size_t i = 0; i < N; ++i)
                result += values[i] * vec[i];
            return result;
        }

        float norm() const
        {
            float result = 0;
            for (size_t i = 0; i < N; ++i)
                result += values[i] * values[i];
            return std::sqrt(result);
        }

        Vec<N> normalized() const
        {
            return *this * (1.f / norm());
        }
    };

    using Vec2 = Vec<2>;
    using Vec3 = Vec<3>;
    using Vec4 = Vec<4>;

    struct Material
    {
        float refractive_index = 1;
        Vec4 albedo = {1, 0, 0, 0};
        Vec3 diffuse_color;
        float specular_exponent = 0;
        Material() = default;
        Material(float r, Vec4 a, Vec3 color, float spec)
            : refractive_index(r), albedo(a), diffuse_color(color), specular_exponent(spec) {}
    };

    struct Sphere
    {
        Vec3 center;
        float radius;
        Material material;
        Sphere(const Vec3 &c, float r, const Material &m)
            : center(c), radius(r), material(m) {}
        bool ray_intersect(const Vec3 &orig, const Vec3 &dir, float &t0) const
        {
            Vec3 L = center - orig;
            float tca = L * dir;
            float d2 = L * L - tca * tca;
            if (d2 > radius * radius)
                return false;
            float thc = std::sqrt(radius * radius - d2);
            t0 = tca - thc;
            float t1 = tca + thc;
            if (t0 < 0)
                t0 = t1;
            return t0 >= 0;
        }
    };

    struct Light
    {
        Vec3 position;
        float intensity;
        Light(const Vec3 &p, float i) : position(p), intensity(i) {}
    };

    Vec3 reflect(const Vec3 &I, const Vec3 &N)
    {
        return I - N * 2.f * (I * N);
    }

    Vec3 refract(const Vec3 &I, const Vec3 &N, float refractive_index)
    {
        float cosi = -std::max(-1.f, std::min(1.f, I * N));
        float etai = 1, etat = refractive_index;
        Vec3 n = N;
        if (cosi < 0)
        {
            cosi = -cosi;
            std::swap(etai, etat);
            n = -N;
        }
        float eta = etai / etat;
        float k = 1 - eta * eta * (1 - cosi * cosi);
        return k < 0 ? Vec3{0, 0, 0} : I * eta + n * (eta * cosi - std::sqrt(k));
    }

    bool scene_intersect(const Vec3 &orig, const Vec3 &dir, const std::vector<Sphere> &spheres, Vec3 &hit, Vec3 &N, Material &material)
    {
        float spheres_dist = std::numeric_limits<float>::max();
        for (const auto &sphere : spheres)
        {
            float dist_i;
            if (sphere.ray_intersect(orig, dir, dist_i) && dist_i < spheres_dist)
            {
                spheres_dist = dist_i;
                hit = orig + dir * dist_i;
                N = (hit - sphere.center).normalized();
                material = sphere.material;
            }
        }
        return spheres_dist < 1000;
    }

    Vec3 cast_ray(const Vec3 &orig, const Vec3 &dir, const std::vector<Sphere> &spheres, const std::vector<Light> &lights, size_t depth = 0)
    {
        Vec3 point, N;
        Material material;
        if (depth > 4 || !scene_intersect(orig, dir, spheres, point, N, material))
        {
            return Vec3{0.2, 0.7, 0.8};
        }

        Vec3 reflect_dir = reflect(dir, N).normalized();
        Vec3 refract_dir = refract(dir, N, material.refractive_index).normalized();
        Vec3 reflect_orig = reflect_dir * N < 0 ? point - N * 1e-3 : point + N * 1e-3;
        Vec3 refract_orig = refract_dir * N < 0 ? point - N * 1e-3 : point + N * 1e-3;
        Vec3 reflect_color = cast_ray(reflect_orig, reflect_dir, spheres, lights, depth + 1);
        Vec3 refract_color = cast_ray(refract_orig, refract_dir, spheres, lights, depth + 1);

        float diffuse_light_intensity = 0, specular_light_intensity = 0;
        for (const auto &light : lights)
        {
            Vec3 light_dir = (light.position - point).normalized();
            float light_distance = (light.position - point).norm();
            Vec3 shadow_orig = light_dir * N < 0 ? point - N * 1e-3 : point + N * 1e-3;
            Vec3 shadow_pt, shadow_N;
            Material tmp_material;
            if (scene_intersect(shadow_orig, light_dir, spheres, shadow_pt, shadow_N, tmp_material) &&
                (shadow_pt - shadow_orig).norm() < light_distance)
                continue;
            diffuse_light_intensity += light.intensity * std::max(0.f, light_dir * N);
            specular_light_intensity += std::pow(std::max(0.f, -reflect(-light_dir, N) * dir), material.specular_exponent) * light.intensity;
        }

        return material.diffuse_color * diffuse_light_intensity * material.albedo[0] +
            Vec3{1., 1., 1.} * specular_light_intensity * material.albedo[1] +
            reflect_color * material.albedo[2] +
            refract_color * material.albedo[3];
    }

    void render(const std::vector<Sphere> &spheres, const std::vector<Light> &lights)
    {
        const int width = 640, height = 480;
        const float fov = 90.f / 180.f * 3.1415926f;

        std::ofstream ofs("./out.ppm", std::ios::binary);
        ofs << "P6\n"
            << width << " " << height << "\n255\n";

        for (size_t pix = 0; pix < width * height; ++pix)
        {
            size_t i = pix % width;
            size_t j = pix / width;
            float x = (2.f * (i + 0.5f) / width - 1) * tan(fov / 2.f);
            float y = -(2.f * (j + 0.5f) / height - 1) * tan(fov / 2.f) * height / width;
            Vec3 dir = Vec3{x, y, 1}.normalized();
            Vec3 color = cast_ray(Vec3{0, 0, 0}, dir, spheres, lights);
            float max = std::max(color[0], std::max(color[1], color[2]));
            if (max > 1)
                color = color * (1.0f / max);
            ofs << (char)(color[0] * 255) << (char)(color[1] * 255) << (char)(color[2] * 255);
        }
    }

    int main()
    {
        Material ivory(1.0, Vec4{0.6, 0.3, 0.1, 0.0}, Vec3{0.4, 0.4, 0.3}, 50.);
        Material glass(1.5, Vec4{0.0, 0.5, 0.1, 0.8}, Vec3{0.6, 0.7, 0.8}, 125.);
        Material red_rubber(1.0, Vec4{0.9, 0.1, 0.0, 0.0}, Vec3{0.3, 0.1, 0.1}, 10.);
        Material mirror(1.0, Vec4{0.0, 10.0, 0.8, 0.0}, Vec3{1.0, 1.0, 1.0}, 1425.);

        std::vector<Sphere> spheres = {
            Sphere(Vec3{-1, -1, 12}, 2, ivory),
            Sphere(Vec3{-2, 0.5, 8}, 2, ivory),
            Sphere(Vec3{1.5, -2.5, 18}, 3, red_rubber),
            Sphere(Vec3{2, 4, 14}, 4, mirror),
            Sphere(Vec3{-1, -1.5, 5}, 1, glass)};

        std::vector<Light> lights = {
            Light(Vec3{-10, 20, -20}, 1.5),
            Light(Vec3{30, -70, -25}, 1.8),
            Light(Vec3{40, 20, -30}, 1.7)};

        render(spheres, lights);
        return 0;
    }
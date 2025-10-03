---
name: optimize-database-performance
description: Optimize database performance with comprehensive analysis and improvement strategies
tools: all
createdAt: "2025-10-02T21:27:54.514Z"
updatedAt: "2025-10-02T21:28:09.592Z"
---

# Optimize Database Performance

Analyze and optimize database queries and schema for improved scalability and faster response times.

## Instructions

1. **Database Performance Analysis**
   - Analyze current database performance and identify bottlenecks
   - Review slow query logs and execution plans
   - Assess database schema design and normalization
   - Evaluate indexing strategy and query patterns
   - Monitor database resource utilization (CPU, memory, I/O)

2. **Query Optimization**
   - Create Optimize slow queries and improve execution plans:

   **PostgreSQL Query Optimization:**
   `

   **MySQL Query Optimization:**

3. **Index Strategy Optimization**
   - Design and implement optimal indexing strategy:

   **Index Analysis and Creation:**

   **Index Maintenance Scripts:**

   ```javascript
   // Node.js index analysis tool
   const { Pool } = require("pg");
   const pool = new Pool();

   class IndexAnalyzer {
     static async analyzeUnusedIndexes() {
       const query = `
         SELECT 
           schemaname,
           tablename,
           indexname,
           idx_scan,
           pg_size_pretty(pg_relation_size(indexrelid)) as size
         FROM pg_stat_user_indexes 
         WHERE idx_scan = 0
         AND schemaname = 'public'
         ORDER BY pg_relation_size(indexrelid) DESC;
       `;

       const result = await pool.query(query);
       console.log("Unused indexes:", result.rows);
       return result.rows;
     }

     static async suggestIndexes() {
       const query = `
         SELECT 
           query,
           calls,
           total_time,
           mean_time
         FROM pg_stat_statements 
         WHERE mean_time > 100
         AND query NOT LIKE '%pg_%'
         ORDER BY total_time DESC
         LIMIT 20;
       `;

       const result = await pool.query(query);
       console.log("Slow queries needing indexes:", result.rows);
       return result.rows;
     }
   }
   ```

4. **Schema Design Optimization**
   - Optimize database schema for performance:

   **Normalization and Denormalization:**

   **Partitioning for Large Tables:**

5. **Connection Pool Optimization**
   - Configure optimal database connection pooling:

   **Node.js Connection Pool Configuration:**

   **Database Connection Middleware:**

6. **Query Result Caching**
   - Implement intelligent database result caching:

   ```javascript
   const Redis = require("redis");
   const redis = Redis.createClient();

   class QueryCache {
     static generateKey(query, params) {
       return `query:${Buffer.from(query + JSON.stringify(params)).toString("base64")}`;
     }

     static async get(query, params) {
       const key = this.generateKey(query, params);
       const cached = await redis.get(key);
       return cached ? JSON.parse(cached) : null;
     }

     static async set(query, params, result, ttl = 300) {
       const key = this.generateKey(query, params);
       await redis.setex(key, ttl, JSON.stringify(result));
     }

     static async cachedQuery(query, params = [], ttl = 300) {
       // Try cache first
       let result = await this.get(query, params);
       if (result) {
         return result;
       }

       // Execute query and cache result
       result = await DatabaseManager.executeQuery(query, params);
       await this.set(query, params, result.rows, ttl);

       return result;
     }

     // Cache invalidation by table patterns
     static async invalidateTable(tableName) {
       const pattern = `query:*${tableName}*`;
       const keys = await redis.keys(pattern);
       if (keys.length > 0) {
         await redis.del(keys);
       }
     }
   }
   ```

7. **Database Monitoring and Profiling**
   - Set up comprehensive database monitoring:

   **Performance Monitoring Script:**

   ```javascript
   class DatabaseMonitor {
     static async getPerformanceStats() {
       const queries = [
         {
           name: "active_connections",
           query:
             "SELECT count(*) FROM pg_stat_activity WHERE state = 'active';",
         },
         {
           name: "long_running_queries",
           query: `SELECT pid, now() - pg_stat_activity.query_start AS duration, query 
                   FROM pg_stat_activity 
                   WHERE (now() - pg_stat_activity.query_start) > interval '5 minutes';`,
         },
         {
           name: "table_sizes",
           query: `SELECT relname AS table_name, 
                          pg_size_pretty(pg_total_relation_size(relid)) AS size
                   FROM pg_catalog.pg_statio_user_tables 
                   ORDER BY pg_total_relation_size(relid) DESC LIMIT 10;`,
         },
         {
           name: "index_usage",
           query: `SELECT relname AS table_name, 
                          indexrelname AS index_name,
                          idx_scan AS index_scans,
                          seq_scan AS sequential_scans
                   FROM pg_stat_user_indexes 
                   WHERE seq_scan > idx_scan;`,
         },
       ];

       const stats = {};
       for (const { name, query } of queries) {
         try {
           const result = await pool.query(query);
           stats[name] = result.rows;
         } catch (error) {
           stats[name] = { error: error.message };
         }
       }

       return stats;
     }

     static async alertOnSlowQueries() {
       const slowQueries = await pool.query(`
         SELECT query, calls, total_time, mean_time, stddev_time
         FROM pg_stat_statements 
         WHERE mean_time > 1000 
         ORDER BY mean_time DESC 
         LIMIT 10;
       `);

       if (slowQueries.rows.length > 0) {
         console.warn("Slow queries detected:", slowQueries.rows);
         // Send alert to monitoring system
       }
     }
   }

   // Schedule monitoring
   setInterval(async () => {
     await DatabaseMonitor.alertOnSlowQueries();
   }, 300000); // Every 5 minutes
   ```

8. **Database Vacuum and Maintenance**
   - Implement automated database maintenance:

   **PostgreSQL Maintenance Scripts:**

   ```sql
   -- Automated vacuum and analyze
   CREATE OR REPLACE FUNCTION auto_vacuum_analyze()
   RETURNS void AS $$
   DECLARE
     rec RECORD;
   BEGIN
     FOR rec IN
       SELECT schemaname, tablename
       FROM pg_tables
       WHERE schemaname = 'public'
     LOOP
       EXECUTE 'VACUUM ANALYZE ' || quote_ident(rec.schemaname) || '.' || quote_ident(rec.tablename);
       RAISE NOTICE 'Vacuumed table %.%', rec.schemaname, rec.tablename;
     END LOOP;
   END;
   $$ LANGUAGE plpgsql;

   -- Schedule maintenance (using pg_cron extension)
   SELECT cron.schedule('nightly-maintenance', '0 2 * * *', 'SELECT auto_vacuum_analyze();');
   ```

   **Maintenance Monitoring:**

   ```javascript
   class MaintenanceMonitor {
     static async checkTableBloat() {
       const query = `
         SELECT 
           tablename,
           pg_size_pretty(pg_total_relation_size(tablename::regclass)) as size,
           n_dead_tup,
           n_live_tup,
           CASE 
             WHEN n_live_tup > 0 
             THEN round(n_dead_tup::numeric / n_live_tup::numeric, 2) 
             ELSE 0 
           END as dead_ratio
         FROM pg_stat_user_tables 
         WHERE n_dead_tup > 1000
         ORDER BY dead_ratio DESC;
       `;

       const result = await pool.query(query);

       // Alert if dead tuple ratio is high
       result.rows.forEach((row) => {
         if (row.dead_ratio > 0.2) {
           console.warn(
             `Table ${row.tablename} has high bloat: ${row.dead_ratio}`,
           );
         }
       });

       return result.rows;
     }

     static async reindexIfNeeded() {
       const bloatedIndexes = await pool.query(`
         SELECT indexname, tablename 
         FROM pg_stat_user_indexes 
         WHERE idx_scan = 0 AND pg_relation_size(indexrelid) > 10485760; -- > 10MB
       `);

       // Suggest reindexing unused large indexes
       bloatedIndexes.rows.forEach((row) => {
         console.log(
           `Consider dropping unused index: ${row.indexname} on ${row.tablename}`,
         );
       });
     }
   }
   ```

9. **Performance Testing and Benchmarking**
   - Set up database performance testing:

   **Load Testing Script:**

   ```javascript
   const { Pool } = require("pg");
   const pool = new Pool();

   class DatabaseLoadTester {
     static async benchmarkQuery(query, params, iterations = 100) {
       const times = [];

       for (let i = 0; i < iterations; i++) {
         const start = process.hrtime.bigint();
         await pool.query(query, params);
         const end = process.hrtime.bigint();

         times.push(Number(end - start) / 1000000); // Convert to milliseconds
       }

       const avg = times.reduce((a, b) => a + b, 0) / times.length;
       const min = Math.min(...times);
       const max = Math.max(...times);
       const median = times.sort()[Math.floor(times.length / 2)];

       return { avg, min, max, median, iterations };
     }

     static async stressTest(concurrency = 10, duration = 60000) {
       const startTime = Date.now();
       const results = { success: 0, errors: 0, totalTime: 0 };

       const workers = Array(concurrency)
         .fill()
         .map(async () => {
           while (Date.now() - startTime < duration) {
             try {
               const start = Date.now();
               await pool.query("SELECT COUNT(*) FROM products");
               results.totalTime += Date.now() - start;
               results.success++;
             } catch (error) {
               results.errors++;
             }
           }
         });

       await Promise.all(workers);

       results.qps = results.success / (duration / 1000);
       results.avgResponseTime = results.totalTime / results.success;

       return results;
     }
   }

   // Run benchmarks
   async function runBenchmarks() {
     console.log("Running database benchmarks...");

     const simpleQuery = await DatabaseLoadTester.benchmarkQuery(
       "SELECT * FROM products LIMIT 10",
     );
     console.log("Simple query benchmark:", simpleQuery);

     const complexQuery = await DatabaseLoadTester.benchmarkQuery(
       `SELECT p.*, c.name as category 
        FROM products p 
        JOIN categories c ON p.category_id = c.id 
        ORDER BY p.created_at DESC LIMIT 50`,
     );
     console.log("Complex query benchmark:", complexQuery);

     const stressTest = await DatabaseLoadTester.stressTest(5, 30000);
     console.log("Stress test results:", stressTest);
   }
   ```

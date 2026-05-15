import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;

public class MartinFatherSteps {

    public static long gcd(long a, long b) {
        while (b != 0) {
            long temp = b;
            b = a % b;
            a = temp;
        }
        return a;
    }

    public static Set<Long> getDivisors(long num) {
        Set<Long> divisors = new HashSet<>();
        if (num == 0) return divisors;
        for (long i = 1; i * i <= num; i++) {
            if (num % i == 0) {
                divisors.add(i);
                divisors.add(num / i);
            }
        }
        return divisors;
    }

    public static long modInverse(long a, long m) {
        long m0 = m;
        long y = 0, x = 1;
        if (m == 1) return 0;

        while (a > 1) {
            long q = a / m;
            long t = m;
            m = a % m;
            a = t;
            t = y;
            y = x - q * y;
            x = t;
        }

        if (x < 0) x += m0;
        return x;
    }

    public static long countSolutions(long dPrime, long vPrime, long m, long N) {
        long A = vPrime;
        long B = (-dPrime) % m;
        if (B < 0) B += m;
        long M = m;

        long g = gcd(A, M);
        if (B % g != 0) return 0;

        A /= g;
        B /= g;
        M /= g;

        long invA = modInverse(A, M);
        if (invA == -1) return 0;

        long s0 = (B * invA) % M;
        if (s0 < 0) s0 += M;

        if (s0 > N) return 0;
        return ((N - s0) / M) + 1;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] line1 = br.readLine().split(" ");
        long X1 = Long.parseLong(line1[0]);
        long X2 = Long.parseLong(line1[1]);
        long D = X1 - X2;

        String[] line2 = br.readLine().split(" ");
        long V1 = Long.parseLong(line2[0]);
        long N = Long.parseLong(line2[1]);

        long G = gcd(D, V1);
        long dPrime = D == 0 ? 0 : D / G;
        long vPrime = V1 / G;

        Set<Long> candidates = new HashSet<>();
        int maxSample = 200;
        for (long s = 0; s < maxSample; s++) {
            if (s > N) break;
            long val = dPrime + s * vPrime;
            if (val == 0) continue;
            candidates.addAll(getDivisors(val));
        }

        long maxCount = -1;
        long bestV2 = -1;

        for (long m : candidates) {
            long count = countSolutions(dPrime, vPrime, m, N);
            long currentV2 = G * m;

            if (count > maxCount) {
                maxCount = count;
                bestV2 = currentV2;
            } else if (count == maxCount) {
                if (currentV2 > bestV2) {
                    bestV2 = currentV2;
                }
            }
        }

        System.out.println(maxCount + " " + bestV2);
    }
}

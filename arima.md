# ARIMA Class and Functions #
```
arima_object obj = arima_init(p, d, q, N); // Initialize ARIMA object

// p - Number of Autoregressive coefficients
// d - Number of times the series needs to be differenced
// q - Number of Moving Average Coefficients
// N - Length of Time Series

```

### Execution ###

```
arima_exec(obj, inp);

// obj - ARIMA object
// inp - Input Time series of length N

```

### ARIMA Object Parameters ###

```
        int N;// length of time series
        int Nused;//length of time series after differencing, Nused = N - d
        int method;// See Below
        int optmethod; // See Below
        int p;// size of phi
        int d;// Number of times the series is to be differenced
        int q;//size of theta
        int M; // M = 0 if mean is 0.0 else M = 1
        int ncoeff;// Total Number of Coefficients to be estimated
        double *phi; // Auto-regression Coefficients
        double *theta; // Moving Average Coefficents
        double *vcov;// Variance-Covariance Matrix Of length lvcov
        int lvcov; //length of VCOV
        double *res; // Residuals of size Nused
        double mean; // Mean
        double var; // Variance
        double loglik; // Log Likelihood Value
        double aic; // AIC Criteria value
        int retval; // return value . See Below
```

### ARIMA Method (method) ###

```
0 - Exact Maximum Likelihood Method (Default)
1 - Conditional Method - Sum Of Squares
2 - Box-Jenkins Method
```

### Optimization Method (optmethod) ###

```

optmethod accepts values between 0 and 7 where -

Method 0 - Nelder-Mead
Method 1 - Newton Line Search
Method 2 - Newton Trust Region - Hook Step
Method 3 - Newton Trust Region - Double Dog-Leg
Method 4 - Conjugate Gradient
Method 5 - BFGS
Method 6 - Limited Memory BFGS
Method 7 - BFGS Using More Thuente Method (Default)

```

### Return Values ###

```

retval = 0 Input Error
retval = 1 Probable Success
retval = 4 Optimization Routine didnt converge
retval = 15 Optimization Routine Encountered Inf/Nan Values

```

### Functions associated with ARIMA method ###

```

void arima_setMethod(arima_object obj, int value); // Sets ARIMA method. value accepts 0,1 and 2

void arima_setOptMethod(arima_object obj, int value); // Sets Optimization method. Value accepts 0,1,2,3,4,5,6 and 7

void arima_vcov(arima_object obj, double *vcov); // Returns variance-covariance matrix vcov

void arima_summary(arima_object obj); // Prints ARIMA results and summary.

void arima_predict(arima_object obj, double *inp, int L, double *xpred, double *amse); // L-step Prediction for time series inp (length N). Returns length L output xpred and length L mean square error amse vectors

```

### Deallocate Object Obj when the computation is finished ###
```
 arima_free(obj);
```

## Example ##

| **[Latest Version Of Code Here](https://code.google.com/p/ctsa/source/browse/test/arimatest.c)** |
|:-------------------------------------------------------------------------------------------------|

```

#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "../header/ctsa.h"

int main(void) {
        int i, N, d, L;
        double *inp;
        int p, q;
        double *phi, *theta;
        double *xpred, *amse;
        arima_object obj;
        p = 1;
        d = 0;
        q = 1;


        L = 5;

        phi = (double*)malloc(sizeof(double)* p);
        theta = (double*)malloc(sizeof(double)* q);

        xpred = (double*)malloc(sizeof(double)* L);
        amse = (double*)malloc(sizeof(double)* L);

        FILE *ifp;
        double temp[1200];

        ifp = fopen("seriesA.txt", "r");
        i = 0;
        if (!ifp) {
                printf("Cannot Open File");
                exit(100);
        }
        while (!feof(ifp)) {
                fscanf(ifp, "%lf \n", &temp[i]);
                i++;
        }
        N = i;

        inp = (double*)malloc(sizeof(double)* N);
        //wmean = mean(temp, N);

        for (i = 0; i < N; ++i) {
                inp[i] = temp[i];
                //printf("%g \n",inp[i]);
        }


        obj = arima_init(p, d, q, N);
        arima_setMethod(obj, 0); // Method 0 ("MLE") is default so this step is unnecessary. The method also accepts values 1 ("CSS") and 2 ("Box-Jenkins")
        arima_setOptMethod(obj, 7);// Method 7 ("BFGS with More Thuente Line search") is default so this step is unnecessary. The method also accepts values 0,1,2,3,4,5,6. Check the documentation for details.
        arima_exec(obj, inp);
        arima_summary(obj);
        // Predict the next 5 values using the obtained ARIMA model
        arima_predict(obj, inp, L, xpred, amse);
        printf("\n");
        printf("Predicted Values : ");
        for (i = 0; i < L; ++i) {
                printf("%g ", xpred[i]);
        }
        printf("\n");
        printf("Standard Errors  : ");
        for (i = 0; i < L; ++i) {
                printf("%g ", sqrt(amse[i]));
        }
        printf("\n");
        arima_free(obj);
        free(inp);
        free(phi);
        free(theta);
        free(xpred);
        free(amse);
        return 0;

}

```

![https://lh4.googleusercontent.com/-3it_39tNlFU/VFDNpTP613I/AAAAAAAAAaU/FFf5HihDRBI/s1152/arimatest.png](https://lh4.googleusercontent.com/-3it_39tNlFU/VFDNpTP613I/AAAAAAAAAaU/FFf5HihDRBI/s1152/arimatest.png)
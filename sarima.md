# Seasonal ARIMA Class and Functions #

```

sarima_object sarima_init(int p, int d, int q,int s,int P,int D,int Q, int N);

// p - Number of Autoregressive coefficients
// d - Number of times the series needs to be differenced
// q - Number of Moving Average Coefficients
// N - Length of Time Series
// s - Seasonality/Period
// P - Number of Seasonal Autoregressive coefficients
// D - Number of times the seasonal series needs to be differenced (s*D)
// Q - Number of Seasonal Moving Average Coefficients


```


### Execution ###

```
sarima_exec(obj, inp);

// obj - Seasonal ARIMA object
// inp - Input Time series of length N

```

### Seasonal ARIMA Object Parameters ###

```

        int N;// length of time series
        int Nused;//length of time series after differencing, Nused = N - d - s*D
        int method;// See Below
        int optmethod; //See Below
        int p;// size of phi
        int d;// Number of times the series is to be differenced
        int q;//size of theta
        int s;// Seasonality/Period
        int P;//Size of seasonal phi
        int D;// The number of times the seasonal series is to be differenced
        int Q;//size of Seasonal Theta
        int M; // M = 0 if mean is 0.0 else M = 1
        int ncoeff;// Total Number of Coefficients to be estimated
        double *phi; // Auto-regression Coefficients
        double *theta; // Moving Average Coefficents
        double *PHI; // Seasonal Auto-regression Coefficients
        double *THETA;// Seasonal Moving Average Coefficents
        double *vcov;// Variance-Covariance Matrix Of length lvcov
        int lvcov; //length of VCOV
        double *res; // Residuals of size Nused
        double mean; // mean
        double var; // variance
        double loglik; // log likelihood value
        double aic; // AIC Criteria Value
        int retval; // Return Value. See Below.


```

### Seasonal ARIMA Method (method) ###

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

### Functions associated with Seasonal ARIMA method ###

```

void sarima_setMethod(sarima_object obj, int value); // Sets ARIMA method. value accepts 0,1 and 2

void sarima_setOptMethod(sarima_object obj, int value); // Sets Optimization method. Value accepts 0,1,2,3,4,5,6 and 7

void sarima_vcov(sarima_object obj, double *vcov); // Returns variance-covariance matrix vcov

void sarima_summary(sarima_object obj); // Prints Seasonal ARIMA results and summary.

void sarima_predict(sarima_object obj, double *inp, int L, double *xpred, double *amse); // L-step Prediction for time series inp (length N). Returns length L output xpred and length L mean square error amse vectors

```

### Deallocate Object Obj when the computation is finished ###
```
 sarima_free(obj);
```

## Example ##

| **[Latest Version Of Code Here](https://code.google.com/p/ctsa/source/browse/test/sarimatest.c)** |
|:--------------------------------------------------------------------------------------------------|

```

#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "../header/ctsa.h"

int main(void) {
        int i, N, d, L;
        double *inp;
        int p, q;
        int s, P, D, Q;
        double *phi, *theta;
        double *PHI, *THETA;
        double *xpred, *amse;
        sarima_object obj;
        p = 0;
        d = 1;
        q = 1;
        s = 12;
        P = 0;
        D = 1;
        Q = 1;


        L = 5;

        phi = (double*)malloc(sizeof(double)* p);
        theta = (double*)malloc(sizeof(double)* q);
        PHI = (double*)malloc(sizeof(double)* P);
        THETA = (double*)malloc(sizeof(double)* Q);

        xpred = (double*)malloc(sizeof(double)* L);
        amse = (double*)malloc(sizeof(double)* L);

        FILE *ifp;
        double temp[1200];

        ifp = fopen("seriesG.txt", "r");
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
                inp[i] = log(temp[i]);
                //printf("%g \n",inp[i]);
        }


        obj = sarima_init(p, d, q,s,P,D,Q, N);
        sarima_setMethod(obj, 0); // Method 0 ("MLE") is default so this step is unnecessary. The method also accepts values 1 ("CSS") and 2 ("Box-Jenkins")
        sarima_setOptMethod(obj, 7);// Method 7 ("BFGS with More Thuente Line Search") is default so this step is unnecessary. The method also accepts values 0,1,2,3,4,5,6. Check the documentation for details.
        sarima_exec(obj, inp);
        sarima_summary(obj);
        // Predict the next 5 values using the obtained ARIMA model
        sarima_predict(obj, inp, L, xpred, amse);
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
        sarima_free(obj);
        free(inp);
        free(phi);
        free(theta);
        free(PHI);
        free(THETA);
        free(xpred);
        free(amse);
        return 0;

}


```

![https://lh6.googleusercontent.com/-jbT36lbTsDA/VFF3uNosjKI/AAAAAAAAAak/uotlzg-yApg/s1475/sarimatest.png](https://lh6.googleusercontent.com/-jbT36lbTsDA/VFF3uNosjKI/AAAAAAAAAak/uotlzg-yApg/s1475/sarimatest.png)
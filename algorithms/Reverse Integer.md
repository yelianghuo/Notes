class Solution {
public:
    int reverse(int x) {
        bool outer = true;
        if(x < 0){
            outer = false;
            x = -x;
        } 
            
        int reverse = 0;
        while(x > 0){
            if((reverse * 10 + x % 10) > INT_MAX)
                return 0;
            reverse = reverse * 10 + x % 10;
            
            x = x / 10;
        }
        if(outer == false)
            reverse = -reverse;
        return reverse;
    }
};